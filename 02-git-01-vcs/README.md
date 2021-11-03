# devops-netology
Домашнее задание к занятию «2.1. Системы контроля версий.»

### Описание содержания файла ./terraform/.gitignore

- "#" - строки комментариев
- **/.terraform/* - содержимое папки ".terraform" (где бы она не находилась) будет проигнорировано её содержимое
- *.tfstate, *.tfvars - файлы с расширением .tfstate, .tfvars будут исключены из попадания в репозиторий
- *.tfstate.*, *_override.tf, *_override.tf.json - файлы в имени который есть ".tfstate.", "_override.tf", "_override.tf.json" будут проигнорированы
- crash.log, override.tf, override.tf.json - файлы таким именем будут игнорироваться
- .terraformrc, terraform.rc - файлы конфигурации будут исключены из коммитов