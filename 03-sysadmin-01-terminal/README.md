# Домашнее задание к занятию "3.1. Работа в терминале, лекция 1"  
***

###  1. Установка VirtualBox 6.1.26

### 2. Установка Vagrant 2.2.19

### 3. Отключение Hyper-V
Команды в PowerShell:  
	Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All  
	.\bcdedit.exe /set hypervisorlaunchtype off  

### 4. vagrant init

### 5. Содержимое файла конфигурации VagrantFile:
	Vagrant.configure("2") do |config|
		config.vm.box = "bento/ubuntu-20.04"
		config.vm.provider "virtualbox" do |v|
			v.name = "devops_netology-ubuntu"
			v.memory = 1024
			v.cpus = 2
		end
	end

###### "Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина,
###### которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?"
	RAM:1024mb  
	CPU:1 cpu  
	HDD:64gb  
	video:8mb  

### 6. vagrant up

### 7. vagrant ssh
![Вывод команды vagrant ssh](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-01-terminal/img/vagrant_ssh.PNG "vagrant ssh")  

### 8. Ознакомиться с разделами man bash, почитать о настройках самого bash:
- какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?  
HISTSIZE — количество команд, которые необходимо запоминать в списке истории (по умолчанию — 500); - 807 Строка  

	HISTSIZE  
		The number of commands to remember in the command history (see HISTORY below).  If the value is 0, commands  
		are  not saved in the history list.  Numeric values less than zero result in every command being  
		saved on the history list (there is no limit).  The shell sets the default value to 500  after  reading  
		any startup files.  

- что делает директива ignoreboth в bash?  
ignoreboth - сочетает действие опций ignorespace и ignoredups. - 783 Строка  
			  
	HISTCONTROL  
		A  colon-separated  list of values controlling how commands are saved on the history list.  If the list  
		of values includes ignorespace, lines which begin with a space character are not saved in  the  history  
		list.  A value of ignoredups causes lines matching the previous history entry to not be saved.  A value  
		of ignoreboth is shorthand for ignorespace and ignoredups.  A value of erasedups  causes  all  previous  
		lines  matching  the  current  line to be removed from the history list before that line is saved.  Any  
		value not in the above list is ignored.  If HISTCONTROL is unset, or does not include  a  valid  value,  
		all  lines  read by the shell parser are saved on the history list, subject to the value of HISTIGNORE.  
		The second and subsequent lines of a multi-line compound command are not tested, and are added  to  the  
		history regardless of the value of HISTCONTROL.  
		 
### 9. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано? - Строка 202
{} -  зарезервированные слова, список, список команд в отличии от ") исполнятся в текущей среде интерпретара,  
используется в различных условных циклах, условных операторах, или ограничивает тело функции. В командах выполняет  
подстановку элементов из списка, например,конструкция a{d,c,b}e заменяется на 'ade ace abe'.  

	{ list; } - Строка 202  
         list is simply executed in the current shell environment.  list must be terminated with  a  newline  or
         semicolon.  This is known as a group command.  The return status is the exit status of list.  Note that
         unlike the metacharacters ( and ), { and } are reserved words and must occur where a reserved  word  is
         permitted  to be recognized.  Since they do not cause a word break, they must be separated from list by
         whitespace or another shell metacharacter.
			  
### 10. Основываясь на предыдущем вопросе, как создать однократным вызовом touch 100000 файлов? А получилось ли создать 300000?
Если нет, то почему?Основываясь на предыдущем вопросе, как создать однократным вызовом touch 100000 файлов?  
А получилось ли создать 300000? Если нет, то почему?  
	touch {00001..10000}.txt или touch {00001..30000}.txt - создаст 10000/30000 файлов  

![Команда touch](https://github.com/Bura-M/devops-netology/blob/main/03-sysadmin-01-terminal/img/touch_30000.PNG "touch")

### 11. В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]]?
[[ -d /tmp ]] - возвращает статус условия в '-d /tmp' равное 0 или 1. 1 - папка существует.  

	[[ expression ]]
         Return a status of 0 or 1 depending on the evaluation of the conditional  expression  expression.   
	 Expressions  are composed of the primaries described below under CONDITIONAL EXPRESSIONS.  
	 Word splitting and pathname expansion are not performed on the words between the [[ and ]]; 
	 tilde expansion, parameter and variable expansion, arithmetic expansion, command substitution, 
	 process substitution, and quote removal are performed.  Conditional operators such as -f must be 
	 unquoted to be recognized as primaries.
         When used with [[, the < and > operators sort lexicographically using the current locale.

	 When  used  with  [[, the < and > operators sort lexicographically using the current locale.  The test command
         -d file
         True if file exists and is a directory.
			  
### 12. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах,
которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:  
	
	«vagrant@vagrant:~$ mkdir /tmp/new_path_directory»  
	«vagrant@vagrant:~$ cp /bin/bash /tmp/new_path_directory/»  
	«vagrant@vagrant:~$ PATH=/tmp/new_path_directory:$PATH»  
	«vagrant@vagrant:~$ type -a bash»  
	«bash is /tmp/new_path_directory/bash»  
	«bash is /usr/bin/bash»  
	«bash is /bin/bash»  

### 13. Чем отличается планирование команд с помощью batch и at?
Команда at используется для назначения одноразового задания на заданное время,  
а команда batch — для назначения одноразовых задач, которые должны выполняться, когда загрузка системы становится меньше 0,8.  

### 14. vagrant suspend
