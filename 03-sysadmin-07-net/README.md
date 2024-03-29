# Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"

### 1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
В WIndows:  

	ipconfig /all  

В Ubuntu:  

	ip -br link show  
	cat /proc/net/dev  
    ls -l /sys/class/net/  


### 2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
Протокол LLDP - link layer discovery protocol, протокол канального уровня для обмена информацией о сетевых устройствах. Нужна поддержка на всех устройствах для успешного обмена такой информацией.  
Команда lldpctl. Схожий вывод у команды ip neighbour.  

	apt install lldpd  
	systemctl enable lldpd && systemctl start lldpd  
	lldpctl  

Также протокол ARP, который позволяет узнать аппаратный (MAC) адрес по известному ip-адресу. В Linux есть команда arp, которая умеет показывать список известных на нашем хосте адресов.  

### 3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
VLAN - тегирование фреймов на канальном уровне для того, чтобы определеить их принадлежность к определенной канальной среде, описан в стандарте 802.1q. Поддержка в Linux обеспечивается на уровне ядра - модулем 8021q, который должен быть загружен:  

	lsmod | grep 8021q  
	modprobe 8021q  
	
В Ubuntu для работы с VLAN нужно __установить пакет vlan__:  

	sudo apt-get install vlan  

__Настройка VLAN:__  

	vconfig add eth0 5  
	cat /proc/net/vlan/eth0.5  
	
Команда vconfig add создает на интерфейсе eth0 VLAN-устройство, в результате чего появляется интерфейс eth0.5.    	
Аналогично можно __создать VLAN c помощью команды ip__:  

	ip link add link eth0 name eth0.5 type vlan id 5  
	ip -d link show eth0.5  
	
После перезагрузки системы этот интерфейс будет удален.	Для того, чтобы при запуске системы применялись нужные параметры используется специальный скрипт со всеми сетевыми настройками: etc/network/interfaces  
Пример файла конфигурации:  

	auto eth0.5  
	iface eth0.5 inet static  
	address 192.168.0.1  
	netmask 255.255.255.0  
	vlan-raw-device eth0  

Далее необходимо перезагрузитб=ь службу (sudo systemctl restart networking) или перезагрузить хост.  

### 4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
В Linux агрегация нескольких каналов называется bonding, в современных версиях реализована поддержка модулем ядра. Также есть альтернативный вариант, который называется Network Teaming, который из себя представляет мини драйвер уровня ядра, взаимодействуя с которым через API приложения могут реализовать свою логику работы с несколькими агрегированными интерфейсами. Агрегация используется для повышения пропускной спосбоности и/или улучшения отказоустойчивости сети.  
Типы агрегации (иначе говоря режимы, mode, объединения нескольких каналов):  
* Mode-0(balance-rr) – Данный режим используется по умолчанию. Balance-rr обеспечивается балансировку нагрузки и отказоустойчивость. В данном режиме сетевые пакеты отправляются “по кругу” (round robin - карусель), по очереди перебирая все агрегированные интерфейсы. Если выходят из строя интерфейсы, пакеты отправляются на остальные оставшиеся.  
* Mode-1(active-backup) – Один из интерфейсов работает в активном режиме, остальные в простаивают в резерве. При обнаружении проблемы на активном интерфейсе производится переключение на ожидающий интерфейс.  
* Mode-2(balance-xor) – Передача пакетов распределяется по аппаратному адресу получателя по формуле ((MAC src) XOR (MAC dest)) % число интерфейсов. Режим дает балансировку нагрузки и отказоустойчивость. Так как привязан к MAC-адресу - чаще всего используется в одной локальной сети.  
* Mode-3(broadcast) – Происходит передача во все объединенные интерфейсы, тем самым обеспечивая отказоустойчивость. Рекомендуется только для использования MULTICAST трафика.  
* Mode-4(802.3ad) – динамическое объединение одинаковых портов. В данном режиме можно значительно увеличить пропускную способность входящего так и исходящегот трафика. Для данного режима необходима поддержка и настройка коммутатора/коммутаторов. Этот режим используется когда необходимо настроить  работу через LACP протокол.  
* Mode-5(balance-tlb) – Адаптивная балансировки нагрузки трафика. Входящий трафик получается только активным интерфейсом, исходящий распределяется в зависимости от текущей загрузки канала каждого интерфейса.  
* Mode-6(balance-alb) – Продвинутая адаптивная балансировка нагрузки. Отличается более совершенным алгоритмом балансировки нагрузки чем Mode-5). Обеспечивается балансировку нагрузки как исходящего так и входящего трафика.  
        
Помимо режима работы, основные параметры (опции) конфигурации следующие:  
bond_miimon 100 - частота мониторинга в мс, с которой отслеживать интерфейсы  
bond_downdelay 200 - в мс, должно быть кратно miimon. Устанавливает время ожидания до отключения интерфейса в случае невозможности установить соединение  
bond_updelay 200 - в мс, должно быть кратно miimon. Устанавливает время ожидания до включения интерфейса после восстановления подключения  
        
Настройка bond:  

	sudo modprobe bonding  
	sudo apt-get install ifenslave  
	sudo ip link add bond0 type bond mode 802.3ad  
	sudo ip link set eth0 master bond0  
	sudo ip link set eth1 master bond0  
	sudo nano /etc/network/interfaces  

	# The primary network interface  
	auto bond0  
	iface bond0 inet static  
		address 192.168.1.150  
		netmask 255.255.255.0    
		gateway 192.168.1.1  
		dns-nameservers 192.168.1.1 8.8.8.8  
		dns-search domain.local  
			slaves eth0 eth1  
			bond_mode 0  
			bond-miimon 100  
			bond_downdelay 200  
			bound_updelay 200  
			
	sudo systemctl restart networking.service  
	sudo ifdown eth0 && ifdown eth1 && ifup bond0  
	ifconfig или ip addr  

### 5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
В сети с маской /29 максимальное количество хостов = 6.  
Команда:  

	ipcalc 0.0.0.0/29 | grep Hosts  

Из маски /24 получиться 32 подсети.  

### 6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
100.64.0.0/10  

### 7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?
Вывести таблицу с текущими значениями и на Windows и на Linux можно командой:  
    
	arp -a   
        
Удалить конкретный адрес из таблицы также можно командой:  
	
	arp -d ip_address
	arp -d *  
	
arp -d * - позволит удалить все сохраненные адреса.
        
В Windows также есть возможность очистить все адреса другой командой:  

    netsh interface ip delete arpcache  
        
В Linux альтернатива: 
 
    sudo ip neigh flush all  

