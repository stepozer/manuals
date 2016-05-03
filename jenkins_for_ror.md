# Jenkins CI for Ruby On Rails application

Jenkins позволяет делать автоматические сборки проекта на любом языке программированния. Данный мануал написан для сборки проектов на Ruby On Rails, однако он подходит и для PHP проектов.

Сначала нужно установить Jenkins. На Ubuntu это делается командой:
```sh
apt-get install jenkins
```

Для деплоя и сборки проекта нам понадобятся следующие плагины:

- Post-Build Script Plug-in - возможность запуска post build скриптов
- Publish Over SSH - возможность запуска SSH команд
- Role-based Authorization Strategy - система ролей для авторизации нескольких разработчиков в Jenkins
- RubyMetrics plugin for Jenkins - отображение code coverage отчета в Jenkins

Предполагается что у нас на сервере с приложением находится скрипт capistrano для деплоя. Т.е. удаленный сервер может задеплоить сам на себя.

Алгоритм развертывания приложения:

- Jenkins запускает деплой через capistrano на сервере куда мы разворачиваем приложение. Пример скрипта:
```sh
#!/bin/bash
cd $(dirname $0)
/usr/local/rvm/bin/rvm default do cap staging deploy
```
- Jenkins запускает сприпт для прогона тестов и для проверки code coverage. Тесты гоняются на сервере куда мы задеплоили приложение:

```sh
#!/bin/bash
source /usr/local/rvm/scripts/rvm
rvm use rvmname
cd /var/www/app/
rake -f /var/www/app/Rakefile db:clear_test_db db:migrate RAILS_ENV=test
rake -f /var/www/app/Rakefile spec SPEC_OPTS="--format RspecJunitFormatter  --out /var/www/app/coverage/junit.xml"
tar -zcvf /var/www/app/coverage/rcov.tar.gz -C /var/www/app/coverage/ rcov
```
- После того, как Jenkins прогнал тесты на удаленном сервере, необходимо затянуть результаты сборки на Jenkins сервер:
```sh
scp user@server.com:/var/www/app/coverage/rcov.tar.gz ${WORKSPACE}/rcov.tar.gz
scp user@server.com:/var/www/app/coverage/junit.xml ${WORKSPACE}/junit.xml
rm -rf ${WORKSPACE}/rcov
tar -zxvf ${WORKSPACE}/rcov.tar.gz -C ${WORKSPACE}
touch ${WORKSPACE}/junit.xml
```
