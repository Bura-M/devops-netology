# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"  

### 1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
__1. поместите его в автозагрузку,__  
__2. предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),__  
__3. удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.__  

На картинке ниже открыта в браузере на локальной машине страница http://127.0.0.53:9100/metrics (тот же вывод для localhost) + конфигурация node_exporter:  
![Вывод node_exporter](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-04-os/img/node_exporter.PNG "node_exporter web")  

_Изменения в Systemd Unit_  

	nano /etc/systemd/system/node_exporter.service
	[Unit]
	Description=Prometheus Node Exporter
	Wants=network-online.target
	After=network-online.target

	[Service]
	User=node_exporter
	Group=node_exporter
	Type=simple
	EnvironmentFile=-/etc/default/node_exporter
	ExecStart=/usr/local/bin/node_exporter $EXTRA_OPTS

	[Install]
	WantedBy=multi-user.target

На картинке ниже результаты запуска и остановки службы node_exporter:  
![Вывод ne_stopstart](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-04-os/img/ne_stopstart.PNG "node_exporter stop&start")  

### 2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.  
CPU:  
process_cpu_seconds_total 0.04
node_cpu_seconds_total{cpu="0",mode="idle"} 1301.23
node_cpu_seconds_total{cpu="0",mode="system"} 7.24
node_cpu_seconds_total{cpu="0",mode="user"} 5.24  

Memory:  
node_memory_MemAvailable_bytes 7.24082688e+08  
node_memory_MemFree_bytes 6.03082752e+08  
node_memory_SwapFree_bytes 1.027600384e+09  

Network:  
node_netstat_Ip_Forwarding 2  
node_netstat_TcpExt_ListenDrops 0  
node_netstat_Tcp_PassiveOpens 57  
node_netstat_Udp_InErrors 0  
node_network_receive_errs_total{device="eth0"} 0  
node_network_receive_errs_total{device="eth0"} 0  
node_network_transmit_packets_total{device="eth0"} 2211  

Disk:  
node_disk_io_now{device="sda"} 0  
node_disk_read_bytes_total{device="sda"} 2.56234496e+08  
node_disk_write_time_seconds_total{device="sda"} 5.63  

### 3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). После успешной установки:
__* в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с localhost на bind to = 0.0.0.0,__  
__* добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте vagrant reload:__  

![Вывод netdata](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-04-os/img/netdata.PNG "netdata web")  

### 4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?  
Можно, вплоть до версии:  
![Вывод dmesg](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-04-os/img/dmesg.PNG "dmesg")  

### 5. Как настроен sysctl fs.nr_open на системе по умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
Настройка sysctl fs.nr_open по умолчанию (это "системное ограничение" также можно проверить в /proc/sys/fs/*): 

	$ /sbin/sysctl -n fs.nr_open
	1048576  

/proc/sys/fs/file-max - максимальный дескриптор файла (FD), применяемый на уровне ядра, который не может быть превзойден всеми процессами без увеличения. Применяется ulimitна уровне процесса, который может быть меньше, чем file-max.  
ulimit -Sn и ulimit -Hn - покажут текущие мягкие и жеские лимиты.  

![Вывод ulimit](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-04-os/img/ulimit.PNG "ulimit")  

### 6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.  
![Вывод sleep](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-04-os/img/sleep.PNG "sleep")  


### 7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?  
":(){ :|:& };:" - это определяет функцию с именем : , которая вызывает себя дважды (Код: : | : ). Это происходит в фоновом режиме ( & ). После ; определение функции выполнено, и функция : запускается.  
Если переименовать в forkbomb, то будет выглядеть так:  

		forkbomb()  
		{  
			forkbomb | forkbomb &  
		};  
		forkbomb  

Вызов dmesg последним вывел данную строку, думаю, данный механизм помог стабилизировать ОС (до этого была информация о загрузке VM):  
[ 1528.227718] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-3.scope  

Ограничить число количества processes-per-use можно командой ulimit:  

	ulimit -u 50
	
Конечно, это означает, что 1 пользователь может запустить только 50 процессов.  

