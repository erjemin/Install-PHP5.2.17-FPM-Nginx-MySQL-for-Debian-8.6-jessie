# Сборка и настройка допотопного PHP 5.2.17 вместе с FPM под Debian 8.x (Jessie)

Для составления последовательности комманд для установки древеней PHP 5.2.17 понадобился перерыть много мирового интернета и совершить много ошибок. Данный рецепт гарантированно сработает для [Debian 8.6 (Jessie) x64](https://cdimage.debian.org/cdimage/archive/8.6.0/amd64/). К сожалению автора не мог проверить применимость рецепта для всех вариантов [Debian 8.x «Jessie»](https://www.debian.org/releases/jessie/debian-installer/), в частности не получилось собрать PHP 5.2.17 для Debian 8.x «Jessie» i368 (32-битную): не удалось найти соответсвующий ей пакет `libjpeg-dev` (`libjpeg` для разработчиков). Для Debian 9 «Stretch» нехватало еще большего числа библиотек. Если у вас есть рекомендации, по сборке необходимого состава библиотке для этих и других систем, присылайте коммиты или свяжитесь с автором.

С высокой вероятностью данная инструкция, с небольшими поправками на состав библиотек, стработает и для других версий PHP и систем линейки Debian (например, Ubuntu и ее клоны, Raspbian, Noobs и т.д.).

## Подготовка системы для компиляции PHP 5.2.17 и FPM

Для сборки  5.2.17 и FPM из исходников в системы должны быть установлены разработческие версии некоторых пакетов и компилятор-сборщик:

```bash
sudo apt-get install libxml2-dev \
                     libmysqlclient-dev \
                     libcurl4-gnutls-dev \
                     libpng12-dev \
                     libjpeg62-turbo-dev \
                     libjpeg-dev \
                     libxslt1-dev \
                     libbz2-dev \
                     libmcrypt-dev \
                     libmhash-dev \
                     libfcgi-dev \
                     libmhash-dev \
                     make checkinstall
```
Проверяем, что находимся в корне нашей домашней дирректории и получаем архив с исхолниками PHP 5.2.17 из музея php.net:
```bash
cd ~
wget http://museum.php.net/php5/php-5.2.17.tar.gz
```
Разпаковываем архив:
```bash
tar zxf ./php-5.2.17.tar.gz
```
Скачиваем патчи-заплатки безопасности для php-5.2.17 под Debian Jessie:
```bash
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-libxml2.patch
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-openssl.patch
```
Далее необходимо получить FPM -- FastCGI Process Manager («Менеджер процессов FastCGI»). Это альтернативная реализация FastCGI режима в PHP с несколькими дополнительными возможностя-ми, которые обычно используются для высоконагруженных сайтов. В современных версиях модуль FPM входит в состав ядра PHP. Но на момент существования 5.2.17 он сущесвовал в качестве набора патчей от Андрея Нигматулина, которые устраняли ряд проблем, мешающих полноценно использовать PHP в режиме FastCGI.

PHP-FPM используется в основном в связке с Nginx, но для Apache его присутсвие не требуется (со всеми вытекаюзими последствиями деградации производительности FastCGI при высоких нагрузках).
```bash
wget "http://php-fpm.org/downloads/php-5.2.17-fpm-0.5.14.diff.gz" -O php-fpm.diff.gz
```
Перехрдим в папку куда распаковался PHP:
```bash
cd ./php-5.2.17
```
И примняем полученные патчи:
```bash
patch -Np1 -i ../php-5.2.17-libxml2.patch
patch -Np1 -i ../php-5.2.17-openssl.patch
```
Расспаковываем и применяем патичи PHP-FPM:
```bash
zcat ../php-fpm.diff.gz | patch -Np1
```



Далее, т.к. исходники
sudo ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/x86_64-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so

или
sudo ln -s /usr/lib/i386-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/i386-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/i368-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so





sed -i 's/freetype2\/freetype/freetype/' configure


./configure --prefix=/opt/php5.2 --with-config-file-path=/opt/php5.2 --with-mysqli --with-mysql --with-curl --with-gd --with-jpeg-dir --enable-cli --enable-fastcgi --enable-discard-path --enable-force-cgi-redirect --enable-mbstring --with-mcrypt

./configure  --prefix=/usr/local/php-5.2.17 --with-config-file-path=/usr/local/php-5.2.17/etc --with-mcrypt --with-gettext --with-gd --with-jpeg-dir --with-png-dir --with-ttf --with-curl --with-freetype-dir --enable-gd-native-ttf --enable-mbstring --enable-sockets --with-png-dir --with-pdo-mysql --with-zlib  --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --with-mysql=/usr/local/mysql --with-mysqli=/usr/local/mysql/bin/mysql_config

./configure  --prefix=/usr/local/php-5.2.17 --with-config-file-path=/usr/local/php-5.2.17/etc --with-mcrypt --with-gettext --with-gd --with-jpeg-dir --with-png-dir --with-ttf --with-curl --enable-gd-native-ttf --enable-mbstring --enable-sockets --with-png-dir --with-pdo-mysql --with-zlib  --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --with-mysql=/usr/local/mysql --with-mysqli=/usr/bin/mysql_config

./configure  --prefix=/usr/local --with-config-file-path=/usr/local/etc --with-mcrypt --with-gettext --with-gd --with-jpeg-dir --with-png-dir --with-ttf --with-curl --enable-gd-native-ttf --enable-mbstring --enable-sockets --with-png-dir --with-pdo-mysql --with-zlib  --enable-fastcgi --enable-fpm --enable-force-cgi-redirect --with-mysql=/usr/local/mysql --with-mysqli=/usr/bin/mysql_config


хорошая (но старая) инструкция разрешения ошибок компиляции http://denik.od.ua/rasprostranennye_oshibki_pri_kompilirovanii_php Рекомендуемые для инсталяции пакеты немного устарели и не всегда доспуны, но как минимум может служить поводом «куда копать» для разрешения конфликтов

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