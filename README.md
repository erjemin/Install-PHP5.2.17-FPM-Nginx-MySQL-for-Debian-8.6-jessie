 # ������ ����������� PHP 5.2.17 � FPM

���� �������� ������� ��� ���� ������ �� �������������� ������� ����� ���������� �� ���� CMS Joomla 1.0.15. ��� ����� ������������ :



��� ���� �����������, � ����� �������, PHP 5.2.17: 5http://tokarchuk.ru/2016/03/php-5-2-on-ubuntu-14-04/
������� (�� ������) ���������� ���������� ������ ���������� http://denik.od.ua/rasprostranennye_oshibki_pri_kompilirovanii_php ������������� ��� ���������� ������ ������� �������� � �� ������ �������, �� ��� ������� ����� ������� ������� ����� ������� ��� ���������� ����������
��� ��� ������� ������������ �� ������ ������ ����������, ������� ���� ��������� ����� �����������.

sudo apt-get install libxml2-dev libmysqlclient-dev libcurl4-gnutls-dev libpng12-dev libjpeg62-turbo-dev make libxslt1-dev libbz2-dev libmcrypt-dev libmhash-dev libfcgi-dev libmhash-dev libjpeg-dev checkinstall
sudo apt-get install mysql-server

sudo ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/x86_64-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so

���
sudo ln -s /usr/lib/i386-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/i386-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/i368-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so

wget http://museum.php.net/php5/php-5.2.17.tar.gz
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-libxml2.patch
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-openssl.patch
# PHP-FPM
FastCGI Process Manager, "�������� ��������� FastCGI". ��� �������������� ���������� FastCGI ������ � PHP � ����������� ��������������� �����������-��, ������� ������ ������������ ��� ����������������� ������.
���������� PHP-FPM ����������� �� ���� ����� ������ �� ������ �����������, ������� ��������� ��� �������, �������� ���������� ������������ PHP � ������ FastCGI (������ ���������). � ������ PHP 5.3 ����� ������ ������� � ����, � �������������� ����������� PHP-FPM ���������� ������ ��� ����������.
PHP-FPM ������������ � �������� � ������ � Nginx, ��� ��������� Apache.

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
����������� sudoers
apt-get htop

--------
apt-get install nginx
unlink /etc/nginx/sites-enabled/default
mkdir -p $HOME/[����� �����]/logs
mkdir -p $HOME/[����� �����]/config
nano $HOME/[����� �����]/config intranet_old_nginx.conf

���������� ���: http://help.ubuntu.ru/wiki/nginx-phpfpm
#  __  __     __   ___       _                        _
# |__)(_ \  //  \ /   \     | |                      | | OLD/������
# | \ __) \/ \__/  | | _ __ | |_ _ __ __ _ _ __   ___| |_~~~~~~~~~~
# V: 1.0           | || '_ \| __| '__/ _` | '_ \ / _ \ __|
#                 _| || | | | |_| | | (_| | | | |  __/ |_
#                 \___/_| |_|\__|_|  \__,_|_| |_|\___|\__|
# ����������:   ������-���� nginx ��� ����� Intranet RSVO
# ������������: /home/e-serg/intranet-old/config/intranet_old_nginx.conf

# ��������� �������-������ ������� ������ ���������� Nginx
# ��� ������� ����� ���� ��������� ���� �����, �� ����� ���������� ������.
# ���� ������ ����������� ��������� python (django) ������ - �������� �������� upstream

upstream php-fpm {
    # ������������ Unix-������ ��� �������������� � PHP5-FPM ������
    server unix:/home/e-serg/intranet-old/socket/php-fpm.socket;
    # server unix:/var/run/php5-fpm.sock;
    # ����� ����� ������������ ���-����� � ���� ��� ��������������, �� ��� ���������
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

        fastcgi_pass	php-fpm;       # upstream �������������� ��������� fastcgi
        include		fastcgi_params;               # ���������������� ���� fastcgi;
        fastcgi_split_path_info			^(.+?\.php)(/.*)?$;
        # ������ ���������� "$document_root" ����� ������� ����� � ��������� �������� ������� � ��� ���������� (��. http://wiki.nginx.org/Pitfalls)
        fastcgi_param	SCRIPT_FILENAME		$document_root$fastcgi_script_name;
        fastcgi_param	PATH_TRANSLATED		$document_root$fastcgi_script_name;
        # ��. http://trac.nginx.org/nginx/ticket/321
        set		$path_info		$fastcgi_path_info;
        fastcgi_param	PATH_INFO		$path_info;
        # Additional variables
        fastcgi_param	SERVER_ADMIN		email@example.com;
        fastcgi_param	SERVER_SIGNATURE	nginx/$nginx_version;
        fastcgi_index	index.php;
        fastcgi_param	PHP_VALUE "$phpini";
        fastcgi_param	SCRIPT_FILENAME $document_root$1;


        # uwsgi_pass	php-fpm;       # upstream �������������� ��������� uwsgi
        # include	uwsgi_params;               # ���������������� ���� uwsgi;
        # uwsgi_read_timeout	1800;     # ��������� ������� �� Raspbery pi ����� ����� ��������������.
        # uwsgi_send_timeout	200;      # �� ������ ������ ����� ������ � �����

    }
    # ������ ������� � .htaccess � .htpasswd ������
    location ~* "/\.(htaccess|htpasswd)$" {
        deny all;               # ��������� ��� ��� ����
        return 404;             # ������� ��� ������
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

��������� fpm
sudo /usr/local/sbin/php-fpm restart -y /usr/local/etc/php-fpm.conf

����� �������� � ���, ��� ����� ������� � ������ ����������� �����:

ls -la /home/e-serg/intranet-old/socket/php-fpm.socket
����� ������� ������ ���� �srw-rw�-�, �������� �www-data� (������ �www-data� � ���� root), ��������:

srw-rw---- 1 root root 0 ��� 13 17:12 /home/e-serg/intranet-old/socket/php-fpm.socket

sudo service nginx restart

nano $HOME/[����� �����]/html/info.php

<?php
phpinfo();
?>

����� FPM ���������� ������������� ��� ������ �������� ������ Raspberri pi ���������� �������� ���� /etc/rc.local. ��������� ��� �� ��������������:

sudo nano /etc/rc.local

� ����� ����� ��������� �������� exit 0 ��������� � ���� ������� ������� FPM. ������ ���������� �������� ���:

/usr/local/sbin/php-fpm start -y /usr/local/etc/php-fpm.conf
exit 0
������ ����� ������������� ��� ������ � ���������, ��� FPM ���������� � ��� ���� [�����_�����] ����������� � ��������.

Sudo reboot

mysql -u root -p secret_password_mysql_root

CREATE DATABASE intranet DEFAULT CHARACTER SET cp1251 DEFAULT COLLATE cp1251_general_ci;

CREATE DATABASE phones2 DEFAULT CHARACTER SET latin1 DEFAULT COLLATE latin1_swedish_ci;

�����, ����� ���� Django-���������� �� �������� � ����� ��� ��������� �����-������������ root, ������� ������ ������������ ���� [user] � ������� �secret_password_mysql_user�:
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


