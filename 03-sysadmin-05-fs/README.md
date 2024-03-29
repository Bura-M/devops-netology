# Домашнее задание к занятию "3.5. Файловые системы"

### 2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
Жесткая ссылка и файл, для которой она создавалась имеют одинаковые inode. Поэтому жесткая ссылка имеет те же права доступа, владельца и время последней модификации, что и целевой файл. Различаются только имена файлов. Фактически жесткая ссылка это еще одно имя для файла.  

![Вывод ln](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-05-fs/img/link.PNG "ln")  

### 3-14. Порядок выполнения команд + вывод lsblk
_Пересоздание ВМ согласно требованиям задания:_   

	vagrant destroy  
	vagrant up  
	vagrant ssh  

_Разбиение диска /dev/sdb на 2 размерами 2G и 512M:_  

	sudo -i  
	lsblk или sdisk -l для просмотра информации о дисках  
	fdisk /dev/sdb  

n - для создания партиции и w - для записи.  

_Перенос разделов /dev/sdb на /dev/sdbc:_  

	sfdisk -d /dev/sdb | sfdisk /dev/sdc  
	
_Создание RAID0 и RAID1 из дисков размеров 512M и 2G соответственно:_  

	apt-get install mdadm

Команда зануления суперблоков дисков, на случай если ранее они использовались в RAID и осталась служебная информация (в моём случае просто для запоминания):  

	mdadm --zero-superblock --force /dev/sd{b1,c1}

Получила ответ, означающий, что диски ранее не использовались для создания RAID:  
>mdadm: Unrecognised md component device - /dev/sdb1  
>mdadm: Unrecognised md component device - /dev/sdc1  

Удаление метаданных и подписи на дисках:  

	wipefs --all --force /dev/sd{b1,c1}  

	mdadm --create --verbose /dev/md1 -l 1 -n 2 /dev/sd{b1,c1}  
	mdadm --create --verbose /dev/md0 -l 0 -n 2 /dev/sd{b2,c2}  

_Создание 2 независимых PV на получившихся md0 и md1._  

	pvcreate /dev/md0 /dev/md1
	pvdisplay

_Создание общей volume-group для cозданных ранее физических устройств:_  

	vgcreate vg_test /dev/md1 /dev/md0
	vgdisplay -C

_Создание логического тома размеров 100M с расположением в RAID0:_  

	lvcreate -n lv_test -L 100M vg_test /dev/md0

_Форматирование логического тома lv_test ext4:_  

	mkfs.ext4 /dev/vg_test/lv_test

_Монтирование получившегося раздела в папку /tmp/new:_  

	mkdir /tmp/new
	mount /dev/vg_test/lv_test /tmp/new  
	
Для автомантирования:  

	nano /etc/fstab

>/dev/vg_test/lv_test /tmp/new ext4 defaults 0 0  

_Вывод lsblk:_  
![Вывод lsblk](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-05-fs/img/lsblk.PNG "lsblk")   

### 13&15. Протестируйте целостность файла test.gz:  
Ранее файл был загружен командой:  

	wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz  

Проверка целостности:  
![Проверка целостности](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-05-fs/img/gzip.PNG "Проверка целостности")  

### 16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.  

	pvmove /dev/md0  
	или
	pvmove /dev/md0 /dev/md1  

![Вывод pvmove](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-05-fs/img/pvmove.PNG "pvmove")   

### 17. Сделайте --fail на устройство в вашем RAID1 md.
	mdadm /dev/md1 --fail /dev/sdb1
	
### 18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.
![Вывод dmesg](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-05-fs/img/dmesg.PNG "dmesg")  

### 19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:  
![state_test-gzip](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-05-fs/img/state_test-gzip.PNG "state_test-gzip")  

Дополнительные скриншоты есть в папке img, если они необходимы.
 
