 # Сборка допотопного PHP 5.2.17 и FPM

Этот документ родился как плод усилий по восстановлению старого сайта созданного на базе CMS Joomla 1.0.15. Для этого понадобилось :



Для чего понадобился, в числе прочего, PHP 5.2.17: 5http://tokarchuk.ru/2016/03/php-5-2-on-ubuntu-14-04/
хорошая (но старая) инструкция разрешения ошибок компиляции http://denik.od.ua/rasprostranennye_oshibki_pri_kompilirovanii_php Рекомендуемые для инсталяции пакеты немного устарели и не всегда доспуны, но как минимум может служить поводом «куда копать» для разрешения конфликтов
Вот тут полезые рекомендации по набору патчей исходников, которые надо применить перед компиляцией.

sudo apt-get install libxml2-dev libmysqlclient-dev libcurl4-gnutls-dev libpng12-dev libjpeg62-turbo-dev make libxslt1-dev libbz2-dev libmcrypt-dev libmhash-dev libfcgi-dev libmhash-dev libjpeg-dev checkinstall
sudo apt-get install mysql-server

sudo ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/x86_64-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so

или
sudo ln -s /usr/lib/i386-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/i386-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/i368-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so

wget http://museum.php.net/php5/php-5.2.17.tar.gz
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-libxml2.patch
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-openssl.patch
# PHP-FPM
FastCGI Process Manager, "Менеджер процессов FastCGI". Это альтернативная реализация FastCGI режима в PHP с несколькими дополнительными возможностя-ми, которые обычно используются для высоконагруженных сайтов.
Изначально PHP-FPM представлял из себя набор патчей от Андрея Нигматулина, которые устраняли ряд проблем, мешающих полноценно использовать PHP в режиме FastCGI (список улучшений). С версии PHP 5.3 набор патчей включён в ядро, а дополнительные возможности PHP-FPM включаются флагом при компиляции.
PHP-FPM используется в основном в связке с Nginx, без установки Apache.

wget "http://php-fpm.org/downloads/php-5.2.17-fpm-0.5.14.diff.gz" -O php-fpm.diff.gz

tar zxf ./php-5.2.17.tar.gz
cd ./php-5.2.17
# Patches for Debian Jessie
patch -Np1 -i ../php-5.2.17-libxml2.patch
patch -Np1 -i ../php-5.2.17-openssl.patch

# Patch for PHP-FPM
zcat ../php-fpm.diff.gz | patch -Np1


sed -i 's/freetype2\/freetype/freetype/' configure


./configure --prefix=/opt/php5.2 --with-config-file-path=/opt/php5.2 --with-mysqli --with-mysql --with-curl --with-gd --with-jpeg-dir --enable-cli --enable-fastcgi --enable-discard-path --enable-force-cgi-redirect --enable-mbstring --with-mcrypt

./configure  --prefix=/usr/local/php-5.2.17 --with-config-file-path=/usr/local/php-5.2.17/etc --with-mcrypt --with-gettext --with-gd --with-jpeg-dir --with-png-dir --with-ttf --with-curl --with-freetype-dir --enable-gd-native-ttf --enable-mbstring --enable-sockets --with-png-dir --with-pdo-mysql --with-zlib  --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --with-mysql=/usr/local/mysql --with-mysqli=/usr/local/mysql/bin/mysql_config

./configure  --prefix=/usr/local/php-5.2.17 --with-config-file-path=/usr/local/php-5.2.17/etc --with-mcrypt --with-gettext --with-gd --with-jpeg-dir --with-png-dir --with-ttf --with-curl --enable-gd-native-ttf --enable-mbstring --enable-sockets --with-png-dir --with-pdo-mysql --with-zlib  --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --with-mysql=/usr/local/mysql --with-mysqli=/usr/bin/mysql_config

./configure  --prefix=/usr/local --with-config-file-path=/usr/local/etc --with-mcrypt --with-gettext --with-gd --with-jpeg-dir --with-png-dir --with-ttf --with-curl --enable-gd-native-ttf --enable-mbstring --enable-sockets --with-png-dir --with-pdo-mysql --with-zlib  --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --with-mysql=/usr/local/mysql --with-mysqli=/usr/bin/mysql_config


wget https://mail.gnome.org/archives/xml/2012-August/txtbgxGXAvz4N.txt
patch -p0 -b < txtbgxGXAvz4N.txt
make
sudo make install
/opt/php5.2/bin/php -v
PHP 5.2.17 (cli) (built: Dec  7 2017 11:58:07)
Copyright (c) 1997-2010 The PHP Group
Zend Engine v2.2.0, Copyright (c) 1998-2010 Zend Technologies
chmod ugo+rX -R /opt/php5.2/




echo $PATH
PATH=$PATH:/opt/php5.2/bin/
echo $PATH

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

Инструкция тут: http://help.ubuntu.ru/wiki/nginx-phpfpm
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
}


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


