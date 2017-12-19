# Установка и настройка веб-сервера nginx под Debian 8.x (Jessie) для работы с PHP 5.2.17

## Подготовка к установке nginx

На сервере может быть уже установлен другой web-сервер -- **Apache**. Его службы будт мешать web-серверу nginx. Можно настроить. чтобы совместную работу обоих серверов (например один отдает статические файлы, а другой работает со скриптами), но в нашем случае надо просто удалить Apache. Но для начала, выясним, есьт ди какие-нибудь службы Apache в нашей системе (потребуются права администратора):
```bash
sudo dpkg -l | grep apache
```
Если в ответ мв олучим ненулевое число строк с сервисами, то начинаем радикальное истребление Apache.

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
Все. Мы избавились от Apache2

## Установка и настройка веб-сервера nginx

Нас вполне удовлетворит версия nginx «прилитающая» из репозитория нашей операционной системы (Debian, Ubuntu, Raspbian, Noobs и т.д.). Это удобно и не требует лиших телодвижений при обновлений системы. Но возможно, по причиам лучшей производительности, нам потребуется более свежая версия nginx и тогда надо подключить собственые репозиториев nginx. Подключить репозиторий и обновить nginx можно в любой момент (см. <Обновление версии nginx> в конце этой главы), но мы можно сдлеать и сразу.

### Подключение репозиториев nginx

Подключим дополнительный репозиторий самого nginx. Это позволит нам получать обновления немного раньше, чем они попадут в репозиторий нашей системы. Для проверки подлинности подписи nginx-репозитория, и чтобы избавиться от предупреждений об отсутствующем PGP-ключе во время установки пакета, необходимо добавить ключ, которым были подписаны пакеты и репозиторий nginx, в связку ключей программы apt. Загружаем этот ключ в домашнюю папки:
```bash
cd $HOME
wget http://nginx.org/keys/nginx_signing.key
```
И добавим этот ключ в связку ключей наших репозиторив (нужны права администратора):
```bash
sudo apt-key add nginx_signing.key
```
После указываем имя нужного нам дистрибутива в файле со списком репозиториев `/etc/apt/sources.list`. Открываем список на ректирование (нужны права администратора):
```bash
sudo nano /etc/apt/sources.list
```
Для системы под Debian 8.x «Jessie» в конец файла надо добавить:
```
deb http://nginx.org/packages/debian/ jessie nginx
deb-src http://nginx.org/packages/debian/ jessie nginx
```
Для других систем вместо ***jessie*** надо указать соответствующие имя дистрибутива. Кодовые имена дистрибутивов можно указать [на страничке описаний пакетов nginx](http://nginx.org/ru/linux_packages.html#distributions).

Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора nano `Ctrl+X`. Теперь можно проверить обновления репозиториях. Это особенно важно если мы разворачиваем наш проект на новой машинке c девственно-чистой система (нужны права администратора):
```bash
sudo apt-get update
```
Можно, при желании, произвести и upgrade системы  (нужны права администратора):
```bash
sudo apt-get upgrade
```
Удаляем, теперь уже не нужный, PGP-ключ из корня домашней папки и освобождаем место:
```bash
rm $HOME/nginx_signing.key
```

### Установка nginx

Устанавливаем (нужны права администратора):
```bash
sudo art-get install nginx
```
Теперь можно запустить веб-сервер:
```bash
sudo service nginx start
```
Убедиться что nginx работает корректно можно набрав: ***_http://[ip_нашего_серера]_***. Должна отображаться страница: **`Welcome to nginx on Debian!`**

Установка с помощью `sudo art-get install nginx` уже произвела все необходимые настроки автозапуска. Но убедиться, что они работают можно перегрузив компьютер (нужны права администратора):
```bash
sudo reboot
```

Убеждаемся, что nginx запущен, снова набрав ***_http://[ip-нашего-серера]_*** в браузере. Унать какая версия установлена можно набрав команду (нужны права администратора):
```bash
sudo nginx -v
```
Получим сообщение:
> nginx version: nginx/1.12.2






### Настройка nginx для нашего проекта

Теперь надо настроить наш сайта в nginx. Создаем каталоги для хранения логов, конфигурационных файлов и сокета.
```bash
mkdir -p $HOME/[адрес сайта]/logs
mkdir -p $HOME/[адрес сайта]/conf
mkdir -p $HOME/[адрес сайта]/sock
```

Создаём конфигурационный файл `[адрес_сайта]_nginx.conf`:
```bash
nano $HOME/[адрес сайта]/conf/[адрес_сайта]_nginx.conf
```
Со следующим содержанием:
```
#       ___  ___   ___    ___  __  __  ____  ____  ___     ____  __  __
#      / __)(__ \ / __)  / __)(  )(  )(  _ \( ___)(__ \   (  _ \(  )(  )
#     ( (__  / _/( (_-. ( (__  )(__)(  ) _ < )__)  / _/    )   / )(__)(
#      \___)(____)\___/()\___)(______)(____/(____)(____)()(_)\_)(______)
# Конфикурационный файл nginx для сайта [адрес сайта] ([адрес_сайта]_nginx.conf)

# не забываем изменять _user_ на имя нашего пользователя, которому будет разрешено деплоить и перезапускать сайт.

# Описываем апстрим-потоки которые должен подключить Nginx
# Для каждого сайта надо настроить свйо поток, со своим уникальным именем.
# Если будете настраивать несколько python (django) сайтов - измените название upstream

upstream [адрес_сайта]_django {
    # расположение файла Unix-сокет для взаимодействие с uwsgi
    server unix:///home/[user]/[адрес сайта]/sock/[адрес_сайта].sock;
    # также можно использовать веб-сокет (порт) для взаимодействие с uwsgi. Но это медленнее
    # server 127.0.0.1:8001; # для взаимодействия с uwsgi через веб-порт
}

# конфигурируем сервер
server {
    listen      80;                  # порт на котором будет доступен наш сайт
    server_name [адрес сайта];       # доменное имя сайта
    charset     utf-8;               # подировка по умолчанию
    access_log  /home/[user]/[адрес сайта]/logs/[адрес_сайта]_access.log;    # логи с доступом
    error_log   /home/[user]/[адрес сайта]/logs/[адрес_сайта]_error.log;     # логи с ошибками
    client_max_body_size 32M; # максимальный объем файла для загрузки на сайт (max upload size)

    location /media       { alias /home/[user]/[адрес сайта]/classifier-manager/media; }   # Расположение media-файлов Django
    location /static      { alias /home/[user]/[адрес сайта]/classifier-manager/static; }  # Расположение static-файлов Django
    location /robots.txt  { root /home/[user]/[адрес сайта]/classifier-manager/static; }   # Расположение robots.txt
    location /favicon.ico { root /home/[user]/[адрес сайта]/classifier-manager/static; }   # Расположение favicon.ico
    location / {
        uwsgi_pass           [адрес_сайта]_django;       # upstream обрабатывающий обращений
        include              uwsgi_params;               # конфигурационный файл uwsgi;
        uwsgi_read_timeout   1800;     # некоторые запросы на Raspbery pi очень долго обрабатываются. Например, переиндексация.
        uwsgi_send_timeout   200;      # на всякий случай время записи в сокет
        }

    }
```
Проверьте все скобочки и точки с запятой... Они могут создать много неприятностей. Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора nano с помощью `Ctrl+X`.

Эта конфигурация сообщает nginx, каким образом он отдает данные при обращении к **[адрес сайта]** по порту **80**. Так, при обащении статическим и загруженным пользователем файлам, он отдает их из соответсвующих каталогов, а остальные  запросы будут перенаправлятся в Django-приложение через апстрим `[адрес_сайта]_django`, который работает через юникосвкий файл-сокет `unix:///home/[user]/[адрес сайта]/sock/[адрес_сайта].sock`.

По умолчанию файл конфигурации uWSGI нажодится в папке _/etc/nginx/uwsgi_params_ и мы спользуем именно его, но при желании мы может переопределить eго. К слову сказать, в ранних версиях nginx файл _uwsgi_params_ в поставку не входил. Проверьте есть ли он у вас, и при необходимости [загрузите из git-репозитория разработчиков nginx](https://github.com/nginx/nginx/blob/master/conf/uwsgi_params).

Чтобы nginx подключил наш новый файл конфигурации сайта нужно добавьте ссылку на него в каталог `/etc/nginx/sites-enabled/`:
```bash
sudo ln -s $HOME/[адрес сайта]/conf/[адрес_сайта]_nginx.conf /etc/nginx/sites-enabled/
```

Теперь нужно перезагрузить nginx
```bash
sudo service nginx restart
```

В результате статические файлы теперь будут отдаваться в браузер. Так вызвав `http://[адрес сайта]/static/img/cubex.png` получим картинку:

![Что показываю по http://[адрес сайта]/static/img/cubex.png](https://raw.githubusercontent.com/erjemin/classifier-manager/master/static/img/cubex.png "cubex.png")

nginx работает. То что все в нем корректно проверяем командой: `systemctl status nginx.service`

```
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Чт 2016-10-27 19:41:19 MSK; 4min 27s ago
  Process: 20175 ExecStop=/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /run/nginx.pid (code=exited, status=0/SUCCESS)
  Process: 20187 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
  Process: 20182 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
 Main PID: 20190 (nginx)
   Memory: 2.3M
      CPU: 265ms
      CGroup: /system.slice/nginx.service
           ├─20190 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           ├─20191 nginx: worker process
           ├─20192 nginx: worker process
           ├─20193 nginx: worker process
           └─20194 nginx: worker process
```








unlink /etc/nginx/sites-enabled/default
mkdir -p $HOME/[адрес сайта]/logs
mkdir -p $HOME/[адрес сайта]/config
nano $HOME/[адрес сайта]/config intranet_old_nginx.conf

```Инструкция тут: http://help.ubuntu.ru/wiki/nginx-phpfpm
#   ___       _                        _
#  /   \     | |                      | | OLD FOR PHP 5.2.17
#   | | _ __ | |_ _ __ __ _ _ __   ___| |_~~~~~~~~~~~~~~~~~~
#   | || '_ \| __| '__/ _` | '_ \ / _ \ __|
#  _| || | | | |_| | | (_| | | | |  __/ |_
#  \___/_| |_|\__|_|  \__,_|_| |_|\___|\__|
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






# Сборка nginx из исходников

Если очень хочется (например, для работы с бета-версиями), то можно поверх уже установленной версии <накатить> версию из исходников. Сначала устанавливаем пакеты, необходимые для сборки nginx из исходников -- компилятор С++ и библиотеки  (нужны права администратора):
```bash
sudo apt-get install gcc
sudo apt-get install build-essential
sudo apt-get install libpcre3-dev
sudo apt-get install libcurl4-openssl-dev
sudo apt-get install libexpat1-dev
```

Смотрим [на сайте nginx](http://nginx.org/ru/download.html) акутальную стабильную версию, скачиваем ее в домашнюю директорию и расспаковываем исходники. Переходим в папку, в которую все распокавалось. В ней будем собирать пакет. Например:
```bash
cd $HOME
wget http://nginx.org/download/nginx-1.12.2.tar.gz
tar -zxvf nginx-1.12.2.tar.gz
cd nginx-1.12.2
```

В редакторе `nano conf.sh` создаем командный файл:
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
```
Сохраняем файл `Ctrl+O` и `Enter`, а выходим из редактора `Ctrl+X`.

В этот же файл можно добавить дополнительные модуди, если мы хотим сборку с ними. Например, модуль поддержки WebDAV `--add-module=/root/nginx/nginx-webdav-ext/` (естественно, каждый такой модкль перед сборкой тоже надо скачать и разорхивировать). Но нам никакие дополнительные модули не нужны.

Для того чтобы командный `conf.sh` файл  можно было запустить, устанавливаем соответсвующие права, а за тем запускаем его (нужны права администратора):
```bash
chmod +rwx conf.sh
sudo bash conf.sh
```
Если все прошло хорошо, можно приступать с компиляции, и установить собранный nginx в систему (нужны права администратора):
```bash
sudo make
sudo make install
```
Если же полсле `make` ругается на недостаток каких-то модулей в Linux, то придется разобраться и доустановить необходимое. Но долно пройти все гладко. Свежесобранный nginx установлен. Нужно проверить, что веб-сервер запускается:
```bash
sudo service nginx start
```

Убедиться что nginx работает корректно можно набрав: ***_http://[ip_нашего_серера]_***. Должна отображаться страница: **`Welcome to nginx on Debian!`**

Место на компьютере не резиновое, и теперь можем благополучно удалить папку в которой происходила сборка nginx и архив с исходниками:
```bash
rm -R $HOME/nginx-1.10.1
rm $HOME/nginx-1.10.1.tar.gz
```

# Обновление версии nginx


apt-get remove nginx-full nginx-common nginx
apt-get install nginx