### Управление пакетами. Дистрибьюция софта ###

## Домашнее задание ##
Размещаем свой RPM в своем репозитории
Цель: Часто в задачи администратора входит не только установка пакетов, но и сборка и поддержка собственного репозитория. Этим и займемся в ДЗ.
1) создать свой RPM (можно взять свое приложение, либо собрать к примеру апач с определенными опциями)
2) создать свой репо и разместить там свой RPM
реализовать это все либо в вагранте, либо развернуть у себя через nginx и дать ссылку на репо 

* реализовать дополнительно пакет через docker
Критерии оценки: 5 - есть репо и рпм
+1 - сделан еще и докер образ

Во проекте приложен файл вагрант и вспомогательный внешний скрипт, который, собственно, все и делает.
1. Создадим свой RPM
Устанавливаем все необходимые пакеты: 
> yum install -y redhat-lsb-core wget rpmdevtools rpm-build createrepo yum-utils openssl-devel zlib-devel pcre-devel gcc 
Скачиваем src.rpm - 
> wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.18.0-1.el7.ngx.src.rpm
Воспользуемся rpm -i, распакуем src и spec файл: 
> rpm -i /root/nginx-1.18.0-1.el7.ngx.src.rpm
