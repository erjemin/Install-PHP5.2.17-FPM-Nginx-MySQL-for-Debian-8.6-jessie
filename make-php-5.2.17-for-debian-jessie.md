# Сборка и настройка допотопного PHP 5.2.17 вместе с FPM под Debian 8.x (Jessie)

 Для составления последовательности комманд для установки древеней PHP 5.2.17 понадобился перерыть много мирового интернета и совершить много ошибок. Данный рецепт гарантированно сработает для [Debian 8.6 (Jessie) x64](https://cdimage.debian.org/cdimage/archive/8.6.0/amd64/). На момент сборки PHP в систему уже был устанвлен MySQL Server. Конектор к базе входит в ядро PHP и поэтому при сборке PHP нужно как минимум знать расположение UNIX-сокетов для связи с MySQL. Советуем [сделать  установку MySQL перед компиляцией PHP](install-and-adjust-MySQL-fоr-php-5.2.17.md).

 К сожалению автор не смог проверить применимость рецепта для всех вариантов [Debian 8.x «Jessie»](https://www.debian.org/releases/jessie/debian-installer/), в частности не получилось собрать PHP 5.2.17 для 32-битной версии Debian 8.10 «Jessie» i368. Для неё не удалось найти соответсвующий пакет `libjpeg-dev` (`libjpeg` для разработчиков). Для Debian 9 «Stretch» нехватало еще большего числа библиотек. Если у вас есть рекомендации, по сборке необходимого состава библиотек для этих и других систем, присылайте коммиты или свяжитесь с автором.

 С высокой вероятностью данная инструкция, с небольшими поправками на состав библиотек, сработает и для других версий PHP и систем линейки Debian (например, Ubuntu и ее клоны, Raspbian, Noobs и т.д.).

## Подготовка системы для компиляции PHP 5.2.17 и FPM

 Для сборки  5.2.17 и FPM из исходников в системы должны быть установлены разработческие версии некоторых пакетов и Си-компилятор и сборщик:

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
                     gcc \
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
 Далее необходимо получить FPM -- FastCGI Process Manager («Менеджер процессов FastCGI»). Это альтернативная реализация режима FastCGI в PHP с некоторыми дополнительными возможностями, которые, обычно, используются в высоконагруженных системах. В современных реализациях, модуль FPM входит в состав ядра PHP. Но на момент существования 5.2.17 FPM сущесвовал в качестве набора патчей от Андрея Нигматулина, которые устраняли ряд проблем, мешающих полноценно использовать PHP в режиме FastCGI.

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
 Казалось бы все готво для компиляции и сборки. Но увы, за долгие годы как PHP 5.2.17 был актуален многие системные папки и папки библиотек поменяли свои дислокации. Нам придется сделать несколько подмен с помощью сим-линков. Подмены проиходят в системных папках, так что на потребуется права администратора. Для систем `x64` подмены следующие:
```bash
sudo ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/x86_64-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so
```
для 32-битных систем ёш386ё такие подмены:
```bash
sudo ln -s /usr/lib/i386-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/i386-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/i368-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so
```
Осталось сделать еще одну, небольшую, замену подключаемых (для инстукций #include .h-файлов) папок в исходниках:
```bash
sed -i 's/freetype2\/freetype/freetype/' configure
```

## Сборка PHP 5.2.17 и FPM

Теперь все подготволено для сборки PHP и FPM. Можно собирать и установить, в отдельную папочку (это полезно, если предполагается использовать несколько версий PHP и опертивно переключатсь между ними... для этого нам понадобится изменить значения ключей `--prefix` и `--with-config-file-path`, указав в них путь к требуемой папке). Но в нашем случае произведем установку в системную папку (понядобятся системные права):
```bash
./configure \
      --prefix=/usr/local \
      --with-config-file-path=/usr/local/etc \
      --with-mcrypt \
      --with-gettext \
      --with-gd \
      --with-jpeg-dir \
      --with-png-dir \
      --with-ttf \
      --with-curl \
      --enable-gd-native-ttf \
      --enable-mbstring \
      --enable-sockets \
      --with-png-dir \
      --with-pdo-mysql \
      --with-zlib \
      --enable-fastcgi \
      --enable-fpm \
      --enable-force-cgi-redirect \
      --with-mysql=/usr/local/mysql \
      --with-mysqli=/usr/bin/mysql_config
 ```
 Начнется сборка-компиляция. В процессе ее, из-за отсутствия тех или иных библиотек или модулей разработчика, могут возникать ошибки. При возниконовении ошибок выдавается диагностика, с её помощью можно понять каких модулей не хватает. доустанавливать их и пробовать компиляцию повторно. Неплохую (хотя и устаревшую) инструкцию по разрешению ошибок компиляции моно найти [по ссвлке](http://denik.od.ua/rasprostranennye_oshibki_pri_kompilirovanii_php). Скорее всего вы не найдете рекомендованных этой исрукцией библиотек в репозиториях ващих систем, но как минимум она поможет определиться с направлением «куда копать» для поиска рецептов по разрешению конфликтов сборки.

 Успешное окончание сборки завершится сообщением о создании и размещении конфигурационных файлов и благодарностью:
```
Generating files
updating cache ./config.cache
creating ./config.status
creating php5.spec
creating main/build-defs.h
creating scripts/phpize
creating scripts/man1/phpize.1
creating scripts/php-config
creating scripts/man1/php-config.1
creating sapi/cli/php.1
creating sapi/cgi/fpm/fpm_autoconf.h
creating sapi/cgi/fpm/php-fpm.conf
creating sapi/cgi/fpm/php-fpm
creating main/php_config.h
creating main/internal_functions.c
creating main/internal_functions_cli.c
+--------------------------------------------------------------------+
| License:                                                           |
| This software is subject to the PHP License, available in this     |
| distribution in the file LICENSE.  By continuing this installation |
| process, you are bound by the terms of this license agreement.     |
| If you do not agree with the terms of this license, you must abort |
| the installation process at this point.                            |
+--------------------------------------------------------------------+

Thank you for using PHP.
```
На данном этапе все готово для получения исполняемых файлов и устанвоки PHP в систему, но рекомендуется получить и установить еще одну заплатку-патч. Он проверит несклько строк кода на совсместимость и, если нужно, внессет необходимые изменения:
```
wget https://mail.gnome.org/archives/xml/2012-August/txtbgxGXAvz4N.txt
patch -p0 -b < txtbgxGXAvz4N.txt
```
Теперь можно сделать сборку и установить ее в систему (понядобятся системные права):
```
make
sudo make install
```
Т.к. при компиляции вы куазали что устаноку надо произвести в системную папку, то никаких дополнительных настрок (как то, дать права на исполнение программ в папке PHP `chmod ugo+rX -R /opt/php5.2.17/` и добавление ее к переменным пути `PATH=$PATH:/opt/php5.2/bin/') не потребуется. Можем запустить PHP и убедиться, что верси именно та, что требовалось (5.2.17):
```
php -v
```
В ответ получим:
```
PHP 5.2.17 (cli) (built: Dec  7 2017 11:58:07)
Copyright (c) 1997-2010 The PHP Group
Zend Engine v2.2.0, Copyright (c) 1998-2010 Zend Technologies
```

## Настройка FPM и связи с веб-сервером


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
