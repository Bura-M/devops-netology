# Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3"  

### 1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP  

	telnet route-views.routeviews.org  
	Username: rviews  
	show ip route x.x.x.x/32  
	show bgp x.x.x.x/32  
	
Мой публичный адрес: 188.242.172.245, поэтому команды имели вид:  

	show ip route 188.242.172.245  
	show bgp 188.242.172.245 bestpath  
	
![telnet route-views.routeviews.org](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-08-net/img/telnet.PNG)  

### 2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.  
__Создание dummy0 интерфейса в Ubuntu__(загрузка модуля -> добавление интерфейса -> назначение ему ip-адреса -> добаление интерфейс в автозагрузку):  

	vagrant@netology1:~$ sudo modprobe dummy  
	vagrant@netology1:~$ lsmod | grep dummy   
	dummy                  16384  0  
	vagrant@netology1:~$ sudo ip link add dummy0 type dummy  
	vagrant@netology1:~$ sudo ip addr add 10.0.2.2 dev dummy0  
	
	vagrant@netology1:~$ sudo -i
	root@vagrant:~# echo "dummy" >> /etc/modules
	root@vagrant:~# nano /etc/network/interfaces
		auto dummy0
		iface dummy0 inet static
		address 10.0.2.2
		netmask 255.255.255.0
		
	root@vagrant:~# systemctl restart networking
	root@vagrant:~# ifup dummy0  
	
![dummy](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-08-net/img/dummy.PNG)  
	
__Добавление статических маршрутов (ip route add):__  

	vagrant@vagrant:~$ sudo -i
	root@vagrant:~# ip route add 10.0.2.0/24 via 10.0.2.2 dev dummy0
	root@vagrant:~# ip route add 8.8.8.0/24 via 172.28.128.10
	
Вывод команд ip route:  
![ip_route](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-08-net/img/ip_route.PNG)  

### 3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.  
 Проверка открытых портов команды:  
 
	lsof -nP -iTCP  
	ss -plnut  
	netstat -plnt  
	
![lsof TCP](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-08-net/img/lsof_TCP.PNG)  
На картинке видно, что запущены службы, которые слушают 111 (rpcbind), 53(DNS), 22(SSH) TCP порты (ipv4 и ipv6).  

### 4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?  
Вывод для портов UDP аналогичен выводу из пункта 3, за исключением добавления порта 68 (DHCP-клиент).  

![lsof UDP](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-08-net/img/lsof_UDP.PNG)  

### 5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.  
Диаграмма домашней сети:  
![L3](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-08-net/img/L3.png)  
