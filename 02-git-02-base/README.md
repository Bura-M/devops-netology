# Домашнее задание к занятию «2.2. Основы Git»
Ссылки на репозитории на gitlab и bitbucket:
- [bitbucket](https://bura_m@bitbucket.org/bura_m/devops-netology.git)
- [gitlab](https://gitlab.com/bura_m/devops-netology.git)

### Вывод команды "git remote -v" - задание 1:
$ git remote -v

bitbucket       https://bura_m@bitbucket.org/bura_m/devops-netology.git (fetch)

bitbucket       https://bura_m@bitbucket.org/bura_m/devops-netology.git (push)

gitlab  https://gitlab.com/bura_m/devops-netology.git (fetch)

gitlab  https://gitlab.com/bura_m/devops-netology.git (push)

origin  https://github.com/Bura-M/devogips-netology.git (fetch)

origin  https://github.com/Bura-M/devops-netology.git (push)

### Команды, используемые для выполнения задания 2:
git tag v0.0

git tag -a v0.1 -m "tag 0.1"

git push [наименование ветки] --tags

### Команды, используемые для выполнения задания 3:
git log --pretty="%h - %s"

git checkout 47d1796

git switch -c fix

git push -u origin fix

git commit -m "README.md file was changed in the branch fix"

git push [наименование ветки]