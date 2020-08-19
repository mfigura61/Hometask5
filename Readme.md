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
>yum install -y redhat-lsb-core wget rpmdevtools rpm-build createrepo yum-utils openssl-devel zlib-devel pcre-devel gcc  

Скачиваем src.rpm   
>wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.18.0-1.el7.ngx.src.rpm  

Воспользуемся rpm -i, распакуем src и spec файл:   
>rpm -i /root/nginx-1.18.0-1.el7.ngx.src.rpm  

Перейдем в /root/rpmbuild:  

>ls -nh  
total 8.0K  
drwxr-xr-x.  3 0 0   26 Aug 19 12:54 BUILD  
drwxr-xr-x.  2 0 0    6 Aug 19 13:19 BUILDROOT  
drwxr-xr-x.  3 0 0   20 Aug 19 13:19 RPMS  
drwxr-xr-x.  2 0 0 4.0K Aug 19 12:53 SOURCES  
drwxr-xr-x.  2 0 0   24 Aug 19 12:53 SPECS  
drwxr-xr-x.  2 0 0    6 Aug 19 12:54 SRPMS  

В папке SPECS лежит spec-файл, который описывает что и как собирать, внесем изменения в SPECS/nginx.spec ,добавив в секцию %build необходимый нам модуль OpenSSL:  
>%build
./configure %{BASE_CONFIGURE_ARGS} \
    --with-cc-opt="%{WITH_CC_OPT}" \
    --with-ld-opt="%{WITH_LD_OPT}" \
    --with-openssl=/root/rpmbuild/openssl-1.1.1f
make %{?_smp_mflags}
%{__mv} %{bdir}/objs/nginx \
    %{bdir}/objs/nginx-debug
./configure %{BASE_CONFIGURE_ARGS} \
    --with-cc-opt="%{WITH_CC_OPT}" \
    --with-ld-opt="%{WITH_LD_OPT}"
make %{?_smp_mflags}

Установим зависимости - yum-builddep SPECS/nginx.spec, затем собираем - rpmbuild -bb SPECS/nginx.spec  
Видим два собранных пакета:  
>[root@otuslinux rpmbuild]# ls -l /root/rpmbuild/RPMS/x86_64/  
total 2524  
-rw-r--r--. 1 root root  789552 Aug 19 13:19 nginx-1.18.0-1.el7.ngx.x86_64.rpm  
-rw-r--r--. 1 root root 1792396 Aug 19 13:19 nginx-debuginfo-1.18.0-1.el7.ngx.x86_64.rpm  

Устанавливаем rpm пакет  
>yum localinstall -y RPMS/x86_64/nginx-1.18.0-1.el7.ngx.x86_64.rpm  
Запускаем nginx - systemctl start nginx и можем посмотреть с какими параметрами nginx был скомпилирован, выполнив nginx -V  

