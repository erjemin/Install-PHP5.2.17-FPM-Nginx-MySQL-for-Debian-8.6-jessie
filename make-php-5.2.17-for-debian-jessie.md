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