# Сборка и настройка допотопного PHP 5.2.17 вместе с FPM под Debian 8.x (Jessie)

Для составления последовательности команд для установки древней PHP 5.2.17 понадобился перерыть много мирового интернета и совершить много ошибок. Данный рецепт гарантированно сработает для [Debian 8.6 (Jessie) x64](https://cdimage.debian.org/cdimage/archive/8.6.0/amd64/). На момент сборки PHP в систему уже был установлен MySQL Server. Коннектор к базе входит в ядро PHP и поэтому при сборке PHP нужно как минимум знать расположение UNIX-сокетов для связи с MySQL. Советуем [сделать установку MySQL перед компиляцией PHP](install-and-adjust-MySQL-fоr-php-5.2.17.md).

К сожалению, автор не смог проверить применимость рецепта для всех вариантов [Debian 8.x «Jessie»](https://www.debian.org/releases/jessie/debian-installer/), в частности не получилось собрать PHP 5.2.17 для 32-битной версии Debian 8.10 «Jessie» i368. Для неё не удалось найти соответствующий пакет `libjpeg-dev` (`libjpeg` для разработчиков). Для Debian 9 «Stretch» не хватало ещё большего числа библиотек. Если у вас есть рекомендации, по сборке необходимого состава библиотек для этих и других систем, присылайте коммиты или свяжитесь с автором.

С высокой вероятностью данная инструкция, с небольшими поправками на состав библиотек, сработает и для других версий PHP и систем линейки Debian (например, Ubuntu и её клоны, Raspbian, Noobs и т.д.).

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
Проверяем, что находимся в корне нашей домашней директории и получаем архив с исходниками PHP 5.2.17 из музея php.net:
```bash
cd ~
wget http://museum.php.net/php5/php-5.2.17.tar.gz
```
Распаковываем архив:
```bash
tar zxf ./php-5.2.17.tar.gz
```
Скачиваем патчи-заплатки безопасности для php-5.2.17 под Debian Jessie:
```bash
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-libxml2.patch
wget https://github.com/TommyLau/docker-lnmpa/raw/master/php/5.2/php-5.2.17-openssl.patch
```
Далее необходимо получить FPM – FastCGI Process Manager («Менеджер процессов FastCGI»). Это альтернативная реализация режима FastCGI в PHP с некоторыми дополнительными возможностями, которые, обычно, используются в высоконагруженных системах. В современных реализациях, модуль FPM входит в состав ядра PHP. Но на момент существования 5.2.17 FPM существовал в качестве набора патчей от Андрея Нигматулина, которые устраняли ряд проблем, мешающих полноценно использовать PHP в режиме FastCGI.

PHP-FPM используется в основном в связке с Nginx, но для Apache его присутствие не требуется (со всеми вытекающими последствиями деградации производительности FastCGI при нагрузках).
```bash
wget "http://php-fpm.org/downloads/php-5.2.17-fpm-0.5.14.diff.gz" -O php-fpm.diff.gz
```
Переходим в папку куда распаковался PHP:
```bash
cd ./php-5.2.17
```
И применяем полученные патчи:
```bash
patch -Np1 -i ../php-5.2.17-libxml2.patch
patch -Np1 -i ../php-5.2.17-openssl.patch
```
Распаковываем и применяем патчи PHP-FPM:
```bash
zcat ../php-fpm.diff.gz | patch -Np1
```
Казалось бы, всё готово для компиляции и сборки. Но увы, за долгие годы как PHP 5.2.17 был актуален многие системные папки и папки библиотек поменяли свои дислокации. Нам придётся сделать несколько подмен с помощью сим-линков. Подмены происходят в системных папках, так что на потребуется права администратора. Для систем `x64` подмены следующие:
```bash
sudo ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/x86_64-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so
```
для 32-битных систем x386 - такие подмены:
```bash
sudo ln -s /usr/lib/i386-linux-gnu/libjpeg.so.62.0.0 /usr/lib/libjpeg.so
sudo ln -s /lib/i386-linux-gnu/libpng12.so.0.50.0 /usr/lib/libpng.so
sudo ln -s /usr/lib/i368-linux-gnu/libmysqlclient.so.18.0.0 /usr/lib/libmysqlclient.so
```
Осталось сделать ещё одну, небольшую, замену подключаемых (для инструкций #include .h-файлов) папок в исходниках:
```bash
sed -i 's/freetype2\/freetype/freetype/' configure
```

## Сборка PHP 5.2.17 и FPM

Теперь всё подготовлено для сборки PHP и FPM. Можно собирать и установить, в отдельную папочку - это полезно, если предполагается использовать несколько версий PHP и оперативно переключаться между ними (для этого нам понадобится изменить значения ключей `--prefix` и `--with-config-file-path`, указав в них путь к требуемой папке). Но в нашем случае произведём установку в системную папку (понадобятся права администратора):
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
Начнётся сборка-компиляция. В процессе её, из-за отсутствия тех или иных библиотек или модулей разработчика, могут возникать ошибки. При возникновении ошибок выдаётся диагностика, с её помощью можно понять каких модулей не хватает – до устанавливать их и пробовать компиляцию повторно. Неплохую (хотя и устаревшую) инструкцию по разрешению ошибок компиляции моно найти [по ссылке](http://denik.od.ua/rasprostranennye_oshibki_pri_kompilirovanii_php). Скорее всего вы не найдёте рекомендованных этой инструкцией библиотек в репозиториях ваших систем, но как минимум она поможет определиться с направлением «куда копать» для поиска рецептов по разрешению конфликтов сборки.

#### *Примечание 1:*
*Поддержка LDAP в PHP не доступна по умолчанию. Для активации нужно использовать опцию `--with-ldap[=DIR]` при компиляции PHP, где DIR - это каталог установки базы LDAP. Для включения поддержки SASL, убедитесь что использована опция `--with-ldap-sasl[=DIR]` , и что sasl.h существует в системе. (см. [источник](http://php.net/manual/ru/ldap.installation.php))*

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
На данном этапе всё готово для получения исполняемых файлов и установки PHP в систему. Если вы считаете себя гуру, то можете посмотреть и поправить созданные файлы с предлагаемыми настройками для компиляции. Например, если у вас всё ещё не установлен MySQL указать расположение MySQL-сокета.

Теперь рекомендуется получить и установить ещё одну заплатку-патч. Она проверит несколько строк кода на совместимость и, если нужно, внесёт необходимые изменения:
```
wget https://mail.gnome.org/archives/xml/2012-August/txtbgxGXAvz4N.txt
patch -p0 -b < txtbgxGXAvz4N.txt
```
Теперь можно сделать сборку и установить её в систему (понадобятся системные права):
```
make
sudo make install
```
Т.к. при компиляции вы указали, что установку надо произвести в системную папку, то никаких дополнительных настроек (как то, дать права на исполнение программ в папке PHP `chmod ugo+rX -R /opt/php5.2.17/` и добавление её к переменным пути `PATH=$PATH:/opt/php5.2/bin/') не потребуется. Можем запустить PHP и убедиться, что версия именно та, что требовалось (5.2.17):
```
php -v
```
В ответ получим:
```
PHP 5.2.17 (cli) (built: Dec  7 2017 11:58:07)
Copyright (c) 1997-2010 The PHP Group
Zend Engine v2.2.0, Copyright (c) 1998-2010 Zend Technologies
```

## Настройка FPM и связка с веб-сервером

Конфигурационный файл FPM находится в системной папке `/usr/local/etc/` и для его редактирования нужны системные права. По хорошему надо перенести его в папку с config-файлами нашего сайта, но во времена PHP 5.2.17 была другая концепция управления конфигурациями, когда настройки всех PHP-сайтов и -проектов находятся в одном файле. Кроме того, изменился сам формат файла конфигурации. Раньше (т.к. в нашем случае) – был в XML, а современные версии используют стандартный формат `.ini` с представлением `переменная = значение`.

К счастью, у нас будет всего один сайт (он же сайт по умолчанию) и такое хранение файла конфигураций в системной папке никаких неудобств не создаст. Открывает фал на редактирование:
```bash
sudo nano /usr/local/etc/php-fpm.conf
```
Далее находим и меняем в нём и меняем настройки `error_log` – размещение log-файлов FPM (не забываем менять значения для **[user]** и **[site]** в путях к файлам):
```xml
    <value name="error_log">/home/[user]/[site]/logs/php-fpm.log</value>
```
Для `listen_address` указываем расположение сокета для обмена с данными с веб-сервером nginx (можно использовать веб-сокет из конфига по умолчанию – `127.0.0.1:9000` – но unix-сокет производительней). Снова не забываем менять значения для **[user]** и **[site]** указывая пути к файлам:
```xml
    <value name="listen_address">/home/[user]/[site]/socket/php-fpm.socket</value>
    <!--    <value name="listen_address">127.0.0.1:9000</value>     -->
```
Далее нужно изменить значение `mode`, `group` и `mode` блока `listen_options`, указывающих разрешения на чтение и запись для сокета. Т.к. веб-сервер nginx работает от имени «***www-data***» и создаёт одноимённую группу для родственных процессов, то значения будут следующие:
```xml
    <value name="mode">www-data</value>
    <value name="group">www-data</value>
    <value name="mode">0660</value>
```
Далее, чуть ниже, можно сделать аналогичные изменяя для `user` и `group` внешнего блока `pool`. Они указывают от чьего имени будет запускаться процесс FPM. Если их оставить по умолчанию, то процессы будут работать от имени 'root', что нежелательно:
```xml
        <value name="user">www-data</value>
        <value name="group">www-data</value>
```
Всё. Настройки закончены. Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора `Ctrl+X`.

К сожалению, более подробно о настройках старого FPM для PHP 5.2.17 сейчас уже не найти. Даже эти, предложенные выше, настройки были получены через сопоставление настроек современных FPM и предложенного по умолчанию `php-fpm.conf`.

Теперь можно запустить FPM. Т.к. для PHP 5.2.17 он ещё не был сервисом, запустить и остановить его с помощью стандартных команд `service` не получится. Запускается FPM так (необходимы права администратора):
```bash
sudo /usr/local/sbin/php-fpm start -y /usr/local/etc/php-fpm.conf
```
FPM запущен и можно убедится в том, что права доступа к сокету установлены верно:
```bash
ls -la /home/e-serg/intranet-old/socket/php-fpm.socket
```
Права доступа должны быть «*srw-rw---*», владелец «***www-data***», группа «***www-data***». Например:
```
srw-rw---- 1 www-data www-data 0 дек 18 09:01 /home/e-serg/intranet-old/socket/php-fpm.socket
```
Если права установлены неверно, или FPM не запустился, то следует проверить `php-fpm.conf` и попробовать запустить FPM снова. Перезапуск FPM осуществляется командой:
```bash
sudo /usr/local/sbin/php-fpm restart -y /usr/local/etc/php-fpm.conf
```
В некоторых случаях (скорее для перестраховки) следует перезагрузить и nginx:
```bash
sudo service nginx restart
```

## Тестирование работоспособности связки PHP 5.2.17 → FPM → nginx

Теперь можно проверить работоспособность скриптов PHP 5.2.17 на нашем веб-сервере. Создаём файл `info.php` в корневой папке сайта (не забываем менять значения для **[user]** и **[site]** в путях к файлам):
```bash
nano $HOME/[user]/адрес сайта]/html/info.php
```
И записываем в него следующий PHP-оператор:
```php
<?php
 phpinfo();
?>
```
Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора `Ctrl+X`.

Вызываем `info.php` из браузера: *http://[ip-нашего-сервера]/info.php*. Получаем диагностическую страницу PHP.

## Автозапуск FPM при старте сервера

Для версии PHP 5.2.17 не существовало FastCGI Process Manager (FPM) в качестве сервиса. Его нужно стартовать отдельно. Чтобы происходил автоматический запуск FPM при каждой загрузке нашего сервера необходимо изменить системный файл `/etc/rc.local`. Открываем его на редактирование (нужны права администратора):
```bash
sudo nano /etc/rc.local
```
И перед самой последней строчкой `exit 0` вставляем в него команду запуска FPM. Должно получиться примерно так:
```bash
/usr/local/sbin/php-fpm start -y /usr/local/etc/php-fpm.conf
exit 0
```
Сохраняем файл `Ctrl+O` и `Enter`, выходим из редактора `Ctrl+X` и теперь можно перезагрузить наш сервер (потребуются права администратора) и убедиться, что FPM запустился и наш *http://[ip-нашего-сервера]/info.php* открывается в браузере:
```bash
sudo reboot
```
На этом настройка связки ***PHP 5.2.17 → FPM → nginx*** закачена. Переходим к настройкам древней CMS Joomla! 1.0.15.

#### *Примечание 2:*
*Для Debian (Ubuntu и т.п.) можно (или нужно **дополнительно**) установить модуль `php-ldap`. Для нашей старой PHP 5.2.17 этот  модуль из репозиториев недоступен. Найти его тоже не получилось. Нашлись [php-ldap модули для CentOS](http://www.rpm-find.net/linux/rpm2html/search.php?query=php-ldap). Их можно конвертировать в модули для Debian. Сначала нужно установить конвертер `alien` (нужны права администратора):*
```bash
sudo apt-get install alien
```
*Скачиваем нужный для нашей операционной системы и версии PHP модуль (ищем URL на странице по ссылке выше):*
```bash
wget ftp://ftp.pbone.net/mirror/yum.jasonlitka.com/EL5/x86_64/php-ldap-5.2.17-jason.2.x86_64.rpm
```
*Конвертируем и устанавливаем (нужны права администратора):*
```bash
sudo alien --to-deb php-ldap-5.2.17-jason.2.x86_64.rpm
sudo dpkg -i php-ldap_5.2.17-1_amd64.deb
```