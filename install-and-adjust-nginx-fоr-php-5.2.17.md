### Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17

### Предыдущие этапы:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).

# 5: Установка и настройка веб-сервера nginx

## Подготовка к установке nginx

На сервере может быть уже установлен другой web-сервер — **Apache**. Его службы будут мешать web-серверу nginx. Можно настроить совместную работу обоих серверов (например, один отдаёт статические файлы, а другой работает со скриптами), но в нашем случае надо просто удалить Apache. Для начала, выясним, есть ли какие-нибудь службы Apache в нашей системе (потребуются права администратора):
```bash
sudo dpkg -l | grep apache
```
Если в ответ получим ненулевое число строк с сервисами, то начинаем радикальное истребление Apache.

### Удаление веб-сервера Apache

Сначала удаляем все Apache-компоненты (нужны права администратора):
```bash
sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
```
Затем чистим неиспользуемые («подвисшие») и зависимые пакеты, которые не удалилось деинсталлировать с помощью предыдущей команды (требуются права администратора):
```bash
sudo apt-get autoremove
```
Ищем файлы, который остались от Apache (папки, которые не удалились, файлы конфигураций и т.д.):
```bash
whereis apache2
```
Получаем список директорий (чаще всего одну `/etc/apache2`, в которой могли остаться конфигурационные файлы и остальной мусор), смотрим их содержимое, если в них ничего важного нет, то удаляем командой (нужны права администратора):
```bash
sudo rm -rf /etc/apache2
```
Всё. Мы избавились от Apache2

## Установка и настройка веб-сервера nginx

Нас вполне удовлетворит версия nginx «прилетающая» из репозитория нашей операционной системы (Debian, Ubuntu, Raspbian, Noobs и т.д.). Это удобно и не требует лишних телодвижений при обновлении системы. Но nginx развивается очень активно. Помимо постоянного улучшения производительности, начиная с версии ngginx 1.91.11 подерживается динамическое подключение модулей.  Это очень полезная функция. Чтобы получить самую свежую стабильную версию nginx (наилучшей производительности и с максимальным пакетом встроенных моделй) надо подключить собственные репозитории nginx. Подключить репозиторий и обновить nginx можно в любой момент (см. раздел **«Обновление версии nginx»** в конце этой главы), но лучше сделать это и сразу.

### Подключение репозиториев nginx

Подключим дополнительный репозиторий самого nginx. Это позволит нам получать обновления немного раньше, чем они попадут в репозиторий нашей системы. Для проверки подлинности подписи nginx-репозитория, и чтобы избавиться от предупреждений об отсутствующем PGP-ключе во время установки пакета, необходимо добавить ключ, которым были подписаны пакеты и репозиторий nginx, в связку ключей программы apt. Загружаем этот ключ в домашнюю папку:
```bash
cd $HOME
wget http://nginx.org/keys/nginx_signing.key
```
И добавим этот ключ в связку ключей наших репозиториев (нужны права администратора):
```bash
sudo apt-key add nginx_signing.key
```
После указываем имя нужного нам дистрибутива в файле со списком репозиториев `/etc/apt/sources.list`. Открываем список на редактирование (нужны права администратора):
```bash
sudo nano /etc/apt/sources.list
```
Для системы под Debian 8.x «Jessie» в конец файла надо добавить:
```
# Репозитории nginx для авто-обновлений
deb http://nginx.org/packages/debian/ jessie nginx
deb-src http://nginx.org/packages/debian/ jessie nginx
```
Для других систем вместо ***jessie*** надо указать соответствующие имя дистрибутива. Кодовые имена дистрибутивов можно указать [на страничке описаний пакетов nginx](http://nginx.org/ru/linux_packages.html#distributions).

Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора nano `Ctrl+X`. Теперь можно проверить обновления в репозиториях. Это особенно важно если мы разворачиваем наш проект на новой машинке c девственно-чистой системой (нужны права администратора):
```bash
sudo apt-get update
```
Можно, при желании, произвести и upgrade системы (нужны права администратора):
```bash
sudo apt-get upgrade
```


### Установка nginx

Устанавливаем (нужны права администратора):
```bash
sudo apt-get install nginx
```
Теперь можно запустить веб-сервер:
```bash
sudo service nginx start
```
Убедиться что nginx работает корректно можно набрав: ***_http://[ip_нашего_серера]_***. Должна отображаться страница: **`Welcome to nginx on Debian!`**

Установка с помощью `sudo apt-get install nginx` уже произвела все необходимые настройки автозапуска. Но убедиться, что они работают можно, перегрузив компьютер (нужны права администратора):
```bash
sudo reboot
```

Убеждаемся, что nginx запущен, снова набрав ***_http://[ip-нашего-серера]_*** в браузере. Узнать какая версия установлена можно набрав команду (нужны права администратора):
```bash
sudo nginx -v
```
Получим сообщение:
> nginx version: nginx/1.12.2

## Настройка nginx для нашего проекта

Теперь надо настроить наш сайта (он же сайт по умолчанию) в nginx. Создаём каталоги для хранения логов, конфигурационных файлов и сокета (не забываем менять значения для **[user]** в путях к файлам).
```bash
mkdir -p $HOME/[site]/logs
mkdir -p $HOME/[site]/config
mkdir -p $HOME/[site]/socket
mkdir -p $HOME/[site]/html
```
Создаём конфигурационный файл `[site]_nginx.conf`:
```bash
nano $HOME/[site]/config/[site]_nginx.conf
```
Со следующим содержанием (не забываем менять значения для **[user]** и **[site]** в путях к файлам и **[ip-нашего-сервера]** в адресах):
```
#   ___       _                        _
#  /   \     | |                      | | OLD FOR PHP 5.2.17
#   | | _ __ | |_ _ __ __ _ _ __   ___| |_~~~~~~~~~~~~~~~~~~
#   | || '_ \| __| '__/ _` | '_ \ / _ \ __|
#  _| || | | | |_| | | (_| | | | |  __/ |_
#  \___/_| |_|\__|_|  \__,_|_| |_|\___|\__|
# Назначение:   конфиг-файл nginx для сайта под PHP 5.2.17 через FPM
# Расположение: /home/[user]/[site]/config/[site]_nginx.conf

# Описываем апстрим-потоки которые должен подключить nginx
# Для каждого сайта надо настроить свой поток, со своим уникальным именем.

upstream php-fpm {
    # расположение Unix-сокета для взаимодействия с PHP5 через FPM
    server unix:/home/[user]/[site]/socket/php-fpm.socket;
    # также можно использовать веб-сокет и порт для взаимодействия, но это медленнее
    # server 127.0.0.1:12012;
}
# если нужно ловить http по другим портам, то делаем так:
server {
        listen 8080 default_server;                 # слушает 8080 порт
        return 302 http://[ip-нашего-сервера]$request_uri;   # и редиректим всё
}

server {
    listen *:80 default_server;                     # слушаем 80
    server_name default;

    root       /home/[user]/[site]/html;            # папка для php-сайта
    access_log /home/[user]/[site]/logs/access.log; # хранение access-логов
    error_log  /home/[user]/[site]/logs/error.log;  # хранение error-логов

    set $phpini "
        error_log=/home/[user]/[site]/logs/php-errors.log # установки для phpini
    ";

    index index.php index.html index.htm;           # файлы которые web-сервер считает как индексные

    location ~ ^(.*\.php)$ {                        # обработчик файлов с расширение .php
        fastcgi_pass    php-fpm;                    # upstream обрабатывающий обращений FPM (fastcgi)
        include         fastcgi_params;             # конфигурационный файл fastcgi;
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
    }
    # Запрет доступа к .htaccess и .htpasswd файлам
    location ~* "/\.(htaccess|htpasswd)$" {
        deny all;               # запретить всё для всех
        return 404;             # и вернуть код ошибки
    }
}
```
Проверьте все скобочки и точки с запятой... Они могут создать много неприятностей. Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора nano с помощью `Ctrl+X`.

Эта конфигурация сообщает nginx, каким образом он отдаёт данные при обращении к **[адрес сайта]** по портам **8080** и **80**. Так, при обращении статическим и загруженным пользователем файлам, он отдаёт их из соответствующих каталогов, а остальные запросы будут перенаправляться через апстрим `php-fpm` в FPM который запустит PHP на исполнение и вернёт результат, назад через апстрим. Вся эта «балалайка» работает через юниксовкий файл-сокет `unix:///home/[user]/[site]/socket/[site].sock`.

Чтобы nginx подключил наш новый файл конфигурации сайта нужно добавьте ссылку на него в каталог `/etc/nginx/sites-enabled/`: и удалить (или переименовать, в базовых настройках nginx указано, что он реагирует только на настроечные файлы с расширением **.conf** в папке `/etc/nginx/conf.d/`) дефолтный конфиг из базовой настройки (нужны права администратора):
```bash
sudo mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.old
sudo mkdir -p /etc/nginx/sites-enabled
sudo ln -s $HOME/[site]/config/[site]_nginx.conf /etc/nginx/sites-enabled/
```

Теперь нужно перезагрузить nginx
```bash
sudo service nginx restart
```
Должно все пройти гладко. Если выдваетя сообщение о оштбке:

> Job for nginx.service failed. See 'systemctl status nginx.service' and 'journalctl -xn' for details.

Значит в конфигурационном фале допущены ошибки. Для проверки конфига nginx на ошибки, используйм команду:
```bash
sudo nginx -c /home/[user]/[site]/config/[site]_nginx.conf -t
```

В результате статические файлы теперь будут отдаваться в браузер напрямую, а все php-скрипты через апстрим-сокеты перенаправляются в PHP.

nginx работает. То что всё в нём корректно проверяем командой: `systemctl status nginx.service`

```
Loaded: loaded (/lib/systemd/system/nginx.service; enabled)
   Active: active (running) since Пн 2017-12-25 16:00:54 MSK; 1min 42s ago
     Docs: http://nginx.org/en/docs/
  Process: 2035 ExecStop=/bin/kill -s TERM $MAINPID (code=exited, status=0/SUCCESS)
  Process: 2039 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
  Process: 2038 ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
 Main PID: 2042 (nginx)
   CGroup: /system.slice/nginx.service
           ├─2042 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
           └─2043 nginx: worker process
```

Если в выводе есть строка **Active: active (running)**, значит, web-сервер успешно установлен и запущен.

Теперь можно переходить [к сборке из исходников PHP 5.2.17 и настройки его связки с FPM](make-php-5.2.17-for-debian-jessie.md).


-----------------------


# Сборка nginx из исходников

Если очень хочется (например, для работы с бета-версиями), то можно поверх уже установленной версии <накатить> версию из исходников. Сначала устанавливаем пакеты, необходимые для сборки nginx из исходников — компилятор С++ и библиотеки (нужны права администратора):
```bash
sudo apt-get install gcc build-essential libcurl4-openssl-dev libexpat1-dev libsasl2-dev python-dev libldap2-dev libssl-dev libpcre3-dev
```

Смотрим [на сайте nginx](http://nginx.org/ru/download.html) акутальную стабильную версию, скачиваем её в домашнюю директорию и расспаковываем исходники. Переходим в папку, в которую всё распаковалось. В ней будем собирать пакет. Например:
```bash
cd $HOME
wget http://nginx.org/download/nginx-1.12.2.tar.gz
tar -zxvf nginx-1.12.2.tar.gz
cd nginx-1.12.2
```

cd ~ && git clone https://github.com/kvspb/nginx-auth-ldap.git

В редакторе `nano conf.sh` создаём командный файл:
```bash
./configure --sbin-path=/usr/local/sbin \
            --conf-path=/etc/nginx/nginx.conf \
            --error-log-path=/var/log/nginx/error.log \
            --http-log-path=/var/log/nginx/access.log \
            --pid-path=/var/run/nginx.pid \
            --user=www-data \
            --group=www-data \
            --with-http_gzip_static_module \
            --with-http_realip_module \
            --with-http_mp4_module \
            --with-http_flv_module \
            --with-http_dav_module \
            --with-http_secure_link_module \

            --with-http_gzip_static_module\
            --add-module=/home/[user]/nginx-auth-ldap
```
Сохраняем файл `Ctrl+O` и `Enter`, а выходим из редактора `Ctrl+X`.

В этот же файл можно добавить дополнительные модули, если мы хотим сборку с ними. Например, модуль поддержки WebDAV `--add-module=/root/nginx/nginx-webdav-ext/` (естественно, каждый такой модуль перед сборкой тоже надо скачать и разархивировать... [список модулей от самого nginx](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html)). Но нам никакие дополнительные модули не нужны.

Для того чтобы командный `conf.sh` файл  можно было запустить, устанавливаем соответствующие права, а за тем запускаем его (нужны права администратора):
```bash
chmod +rwx conf.sh
sudo bash conf.sh
```
Если всё прошло хорошо, можно приступать с компиляции, и установить собранный nginx в систему (нужны права администратора):
```bash
sudo make
sudo make install
```
Если же полсле `make` ругается на недостаток каких-то модулей в Linux, то придётся разобраться и до установить необходимое. Но должно пройти всё гладко. Свежесобранный nginx установлен. Нужно проверить, что веб-сервер запускается (нужны права администратора):
```bash
sudo service nginx start
```
Убедиться что nginx работает корректно можно набрав: ***_http://[ip_нашего_серера]_***. Должна отображаться страница: **`Welcome to nginx on Debian!`**

Узнать какая версия установлена можно набрав команду (нужны права администратора):
```bash
sudo nginx -v
```
Получим сообщение:
> nginx version: nginx/1.12.2

Место на компьютере не резиновое, и теперь можем благополучно удалить папку в которой происходила сборка nginx и архив с исходниками:
```bash
rm -R $HOME/nginx-1.12.2
rm $HOME/nginx-1.12.2.tar.gz
```

-----------------------

# Обновление версии nginx

Для обновления версии всех пакетов достаточно дать команду (нужны права администратора):
 ```bash
 sudo apt-get update
 ```
Чтобы получить самые последние версии nginx из родных nginx-репозиториев, нужно сначала обновить свой список репоизиториев (см. раздел «**Подключение репозиториев nginx**» в начале этой главы). удалить старую версию nginx (нужны права администратора):
```bash
sudo apt-get remove nginx-full nginx-common nginx
```
А затем установить nginx заново (опять нужны права администратора):
```
sudo apt-get install nginx
```
Проверяем, что веб-сервер запускается (нужны права администратора):
```bash
sudo service nginx start
```
Убедиться что nginx работает корректно можно набрав: ***_http://[ip_нашего_серера]_***. Должна отображаться страница: **`Welcome to nginx on Debian!`**

 Узнать какая версия установлена можно набрав команду (нужны права администратора):
```bash
sudo nginx -v
```
Получим сообщение:
> nginx version: nginx/1.12.2



## Тестирование работоспособности связки PHP 5.2.17 → FPM → nginx

Теперь можно проверить работоспособность скриптов PHP 5.2.17 на нашем веб-сервере. Создаём файл `info.php` в корневой папке сайта (не забываем менять значения для **[user]** и **[site]** в путях к файлам):
```bash
nano $HOME/[user]/[site]/html/info.php
```
И записываем в него следующий PHP-оператор:
```php
<?php
 phpinfo();
?>
```
Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора `Ctrl+X`.

Вызываем `info.php` из браузера: *http://[ip-нашего-сервера]/info.php*. Получаем диагностическую страницу PHP.


--------------

## Следующие этапы:

* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.
* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) — доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).