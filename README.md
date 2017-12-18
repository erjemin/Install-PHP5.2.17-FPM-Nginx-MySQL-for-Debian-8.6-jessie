 # Установка сервера под старые проекты на базе PHP 5.2.17

Этот документ родился как плод усилий по восстановлению работоспособности старого сайта созданного на базе CMS Joomla! 1.0.15, требующего PHP 5.2.17. Возможно подобная задача встанет и перед вами, и тогда эта инструкция маожет помочь.

Установку сервера состояла из следующих мероприятий:

* Установка системы Debian (или аналога) и первичные настройки.
* Настройка SSH.
* Установка и настройка MySQL.
* [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* Установка и настройка веб-сервера nginx.
* Установка и настройка phpMyAdmin и немного настроек Joomla! 1.0.15.




apt-get mc
apt-get sudo
настраиваем sudoers
apt-get htop

--------
apt-get install nginx
unlink /etc/nginx/sites-enabled/default
mkdir -p $HOME/[адрес сайта]/logs
mkdir -p $HOME/[адрес сайта]/config
nano $HOME/[адрес сайта]/config intranet_old_nginx.conf

```Инструкция тут: http://help.ubuntu.ru/wiki/nginx-phpfpm
#  __  __     __   ___       _                        _
# |__)(_ \  //  \ /   \     | |                      | | OLD/СТАРЫЙ
# | \ __) \/ \__/  | | _ __ | |_ _ __ __ _ _ __   ___| |_~~~~~~~~~~
# V: 1.0           | || '_ \| __| '__/ _` | '_ \ / _ \ __|
#                 _| || | | | |_| | | (_| | | | |  __/ |_
#                 \___/_| |_|\__|_|  \__,_|_| |_|\___|\__|
# Назначение:   конфиг-файл nginx для сайта Intranet RSVO
# Расположение: /home/e-serg/intranet-old/config/intranet_old_nginx.conf

# Описываем апстрим-потоки которые должен подключить Nginx
# Для каждого сайта надо настроить свой поток, со своим уникальным именем.
# Если будете настраивать несколько python (django) сайтов - измените название upstream

upstream php-fpm {
    # расположение Unix-сокета для взаимодействия с PHP5-FPM сервер
    server unix:/home/e-serg/intranet-old/socket/php-fpm.socket;
    # server unix:/var/run/php5-fpm.sock;
    # также можно использовать веб-сокет и порт для взаимодействия, но это медленнее
    # server 127.0.0.1:12012;
}
server {
        listen 8080 default_server;
        return 302 http://10.3.1.171$request_uri;
}

server {
    listen *:80 default_server;
    server_name default;

    root       /home/e-serg/intranet-old/html;
    access_log /home/e-serg/intranet-old/logs/access.log;
    error_log  /home/e-serg/intranet-old/logs/error.log;

    set $phpini "
        error_log=/home/e-serg/intranet-old/logs/php-errors.log
    ";

    index index.php index.html index.htm;

    location ~ ^(.*\.php)$ {

        fastcgi_pass	php-fpm;       # upstream обрабатывающий обращений fastcgi
        include		fastcgi_params;               # конфигурационный файл fastcgi;
        fastcgi_split_path_info			^(.+?\.php)(/.*)?$;
        # Вместо переменной "$document_root" можно указать адрес к корневому каталогу сервера и это желательно (см. http://wiki.nginx.org/Pitfalls)
        fastcgi_param	SCRIPT_FILENAME		$document_root$fastcgi_script_name;
        fastcgi_param	PATH_TRANSLATED		$document_root$fastcgi_script_name;
        # См. http://trac.nginx.org/nginx/ticket/321
        set		$path_info		$fastcgi_path_info;
        fastcgi_param	PATH_INFO		$path_info;
        # Additional variables
        fastcgi_param	SERVER_ADMIN		email@example.com;
        fastcgi_param	SERVER_SIGNATURE	nginx/$nginx_version;
        fastcgi_index	index.php;
        fastcgi_param	PHP_VALUE "$phpini";
        fastcgi_param	SCRIPT_FILENAME $document_root$1;


        # uwsgi_pass	php-fpm;       # upstream обрабатывающий обращений uwsgi
        # include	uwsgi_params;               # конфигурационный файл uwsgi;
        # uwsgi_read_timeout	1800;     # некоторые запросы на Raspbery pi очень долго обрабатываются.
        # uwsgi_send_timeout	200;      # на всякий случай время записи в сокет

    }
    # Запрет доступа к .htaccess и .htpasswd файлам
    location ~* "/\.(htaccess|htpasswd)$" {
        deny all;               # запретить все для всех
        return 404;             # вернуть код ошибки
    }
}```


sudo ln -s /home/e-serg/intranet-old/config/intranet_old_nginx.conf /etc/nginx/sites-enabled/

sudo nano /usr/local/etc/php-fpm.conf

      <value name="listen_address">/home/e-serg/intranet-old/socket/php-fpm.socket</value>
      <!--    <value name="listen_address">127.0.0.1:9000</value>     -->


                        Unix user of processes
                        <value name="user">www-data</value>


                        Unix group of processes
                        <value name="group">www-data</value>


<value name="error_log">/home/e-serg/intranet-old/logs/php-fpm.log</value>
<value name="mode">0660</value>

Запускаем fpm
sudo /usr/local/sbin/php-fpm restart -y /usr/local/etc/php-fpm.conf

Можно убедится в том, что права доступа к сокету установлены верно:

ls -la /home/e-serg/intranet-old/socket/php-fpm.socket
Права доступа должны быть «srw-rw—-», владелец «www-data» (группа «www-data» у меня root), например:

srw-rw---- 1 root root 0 дек 13 17:12 /home/e-serg/intranet-old/socket/php-fpm.socket

sudo service nginx restart

nano $HOME/[адрес сайта]/html/info.php

<?php
phpinfo();
?>

Чтобы FPM запускался автоматически при каждой загрузке нашего Raspberri pi необходимо изменить файл /etc/rc.local. Открываем его на редактирование:

sudo nano /etc/rc.local

И перед самой последней строчкой exit 0 вставляем в него команду запуска FPM. Должно получиться примерно так:

/usr/local/sbin/php-fpm start -y /usr/local/etc/php-fpm.conf
exit 0
Теперь можно перезагрузить наш сервер и убедиться, что FPM запустился и наш сайт [адрес_сайта] открывается в браузере.

Sudo reboot

mysql -u root -p secret_password_mysql_root

CREATE DATABASE intranet DEFAULT CHARACTER SET cp1251 DEFAULT COLLATE cp1251_general_ci;

CREATE DATABASE phones2 DEFAULT CHARACTER SET latin1 DEFAULT COLLATE latin1_swedish_ci;

Затем, чтобы наше Django-приложение не работало с базой под аккаунтом супер-пользователя root, создаем нового пользователя базы [user] с паролем «secret_password_mysql_user»:
GRANT ALL PRIVILEGES ON intranet.* TO 'e-serg'@'localhost' IDENTIFIED BY 'qwaseR12';
GRANT ALL PRIVILEGES ON phones2.* TO 'e-serg'@'localhost' IDENTIFIED BY 'qwaseR12';

SHOW VARIABLES LIKE '%time_zone%';

quit;




$mosConfig_live_site = 'http://10.3.2.171';
$mosConfig_absolute_path = '/home/e-serg/intranet-old/html';
$mosConfig_cachepath = '/home/e-serg/intranet-old/html/cache';
$mosConfig_db = 'intranet';
$mosConfig_password = 'qwaseR12';
$mosConfig_user = 'e-serg';



sudo apt-get install ntp


