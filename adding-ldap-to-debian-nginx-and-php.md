# Добавляем поддрежку Active Directory через LDAP для Debian, nginx и PHP 5.2.17

## Включаем поддрежку LDAP в Debian

и проверка: https://wiki.it-kb.ru/unix-linux/linux-cli-tools/openldap-ldap-client-check-connection-to-active-directory-domain-controller-with-ldapsearch
накатить:
sudo apt-get install ldap-utils
//sudo apt-get install libnss-ldapd (кажется не нужен)
//спросит. Сказать "ldap://msk.rsvo.local" и "DC=msk,DC=rsvo,DC=local"

ldapsearch -H "ldap://msk.rsvo.local" -b "OU=Пользователи домена,DC=msk,DC=rsvo,DC=local" -D "webuser@msk.rsvo.local" -w 'qwe123!@#'

LDPA работает.
Что нужно знать о jessie
5.1 Поддержка LDAP
Особенность в криптографических библиотеках, используемая в библиотеках LDAP, приводит к тому, что программы, использующие LDAP и пытающиеся изменить их эффективные привилегии, не могут соединиться с сервером LDAP с помощью TLS или SSL. Это может привести к проблемам с setuid-программами в системах, использующими libnss-ldap, наподобие sudo, su или schroot и с setuid-программами, которые выполняют поиск в LDAP, наподобие sudo-ldap.
Рекомендуется заменить пакет libnss-ldap на libnss-ldapd, более новую библиотеку, ко торая использует отдельную службу (nslcd) для всех поисков LDAP. Заменой для libpam-ldap является libpam-ldapd.
Обратите внимание, что libnss-ldapd рекомендует кэширующую службу NSS (nscd), пригодность которой в своём окружении вам нужно оценить до установки. В качестве альтернативы nscd можно рассмотреть unscd.
За дополнительной информацией обратитесь к #566351 (https://bugs.debian.org/566351) и #545414 (https://bugs.debian.org/545414).

https://blog.it-kb.ru/2016/10/15/join-debian-gnu-linux-8-6-to-active-directory-domain-with-sssd-and-realmd-for-authentication-and-configure-ad-domain-security-group-authorization-for-sudo-and-ssh-with-putty-sso/

## Добавляем поддержку LDAP в nginx

Если перед установкой nginx мы добавили его репозитории, и выполнили все правильно, то был установлена версию web-сервер **nginx 1.12.2**. Она поддерживает динамическое подключение модулей. Динамические модули nginx появились вверсии **1.9.11** и потому, если у нас более ранняя версия, необходимо сделать обновление (см. раздел <<Обновление версии nginx>> в главы <<[Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md)>>.

 Проверить версию nginx можно с помощью команды:
 ```bash
sudo nginx -v
 ```
 В ответ получим, что-то типа:
 > nginx version: nginx/1.12.2

 ### Собираем динамический модуль для поддержки LDAP

 Мы узнали свою текущую версию nginx. Для сборки динамического модуля необходимо получить исходные файлы. Получить URL на них можно [на сайте nginx](http://nginx.org/ru/download.html). Скачиваем её в домашнюю директорию и расспаковываем исходники/ Например:
```bash
cd $HOME
wget http://nginx.org/download/nginx-1.12.2.tar.gz
tar -zxvf nginx-1.12.2.tar.gz
```
Теперь необходимо получить исходники nginx-модуля поддержки LDAP (`nginx-auth-ldap`) из репозитория разработчика. Они хранятся на `github`, а значит сначала нужно установить `git` (нужны права администратора):
```bash
sudo apt-get install git
```
Получаем `clone` исходники их репозитория:
```bash
git clone https://github.com/kvspb/nginx-auth-ldap.git
```
Переходим в папку, в которую распаковались исходники nginx:
```bash
cd nginx-1.12.2
```
Для сборки нам понядобится комптлятор С++ (`gcc`) и много модулей разработки (возможно есть и лишние, но так как модули и библиотеки тянут за собой много зависимый, выяснить, что действительно нужно, а что лишнее -- не получилось). Установим их  (нужны права администратора):
```bash
sudo apt-get install gcc \
                     build-essential \
                     libcurl4-openssl-dev \
                     libexpat1-dev \
                     libsasl2-dev \
                     python-dev \
                     libldap2-dev \
                     libssl-dev \
                     libpcre3-dev
```
Для добаления динамических модулей к текущей установке nginx, нам требуется знать — с какими параметрами он был собран. Если собирать с параметрами, в которых указать только новые модули, nginx не позволит использовать такой модуль.

Для того, чтобы узнать, с какими параметрами был установлен nginx (в том числе, из репозитория), нужно набрать команду (нужны права администратора):
```bash
sudo nginx -V
```
Она сообщит, все параметры, которые были испольованы при сборке (не только для собранных в ручную версий, но и для версии <<прилетающей>> в систему из репозиториев. Сообщение выглядит примерно так:

> configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie'

Нам нужно к этим параметрам добавить ключи, собирающие нужные нам модули. В нашем услучае один модуль поддержки LDAP, и в этом ключе нужно указать где находятся исходные файлы для сборки модуля. Команда будет выгляеть приветно так (не забываем менять значения для **[user]** указывая путь к исходникам в последней строчке):
```bash
./configure \
    --prefix=/etc/nginx \
    --sbin-path=/usr/sbin/nginx \
    --modules-path=/usr/lib/nginx/modules \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --lock-path=/var/run/nginx.lock \
    --http-client-body-temp-path=/var/cache/nginx/client_temp \
    --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
    --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
    --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
    --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
    --user=nginx \
    --group=nginx \
    --with-compat \
    --with-file-aio \
    --with-threads \
    --with-http_addition_module \
    --with-http_auth_request_module \
    --with-http_dav_module \
    --with-http_flv_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_mp4_module \
    --with-http_random_index_module \
    --with-http_realip_module \
    --with-http_secure_link_module \
    --with-http_slice_module \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-http_sub_module \
    --with-http_v2_module \
    --with-mail \
    --with-mail_ssl_module \
    --with-stream \
    --with-stream_realip_module \
    --with-stream_ssl_module \
    --with-stream_ssl_preread_module \
    --with-cc-opt='-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' \
    --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' \
    --add-dynamic-module=/home/[user]/nginx-auth-ldap
 ```
Остается собрат:
```bash
make modules
```
И установить (или обновить если таковые были) модули nginx (нужны права администратора):
```bash
sudo make install
```
В конце, устаноdobr сообщит месторасположение файлов nginx. В частности, самого динамического модуля для поддежки LDAP `/usr/lib/nginx/modules/ngx_http_auth_ldap_module.so` и глобального конфигурационного файла nginx `/etc/nginx/nginx.conf`. Отредактируем последний чтобы задействовать собранный и уситановленный модуль. Для этого откроем для редактирования глобальный конфиг nginx (нужны права администратора):
```bash
nano /etc/nginx/nginx.conf
```
И вставим вначале следующие строки:
```
load_module         modules/ngx_http_auth_ldap_module.so;
```
Обращаем внимание на значения `user` -- пользователь от имени которого будет работать web-сервер (**запомним его -- *[nginx-user]*** и `worker_processes`  -- число обработчиков процессов nginx (должно совпадать с числом процессорных ядер нашего компьютера). Кроме того, можно сразу за строкой `include /etc/nginx/conf.d/*.conf;`, указывающей подключаемую папку с насстройками наших сайтов, добавить `include /etc/nginx/sites-enabled/*;`, указывающую подключить еще одну папку с конфигами.

Настройки закончены. Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора `Ctrl+X`.

Теперь создадим эту новую папку с конфигами (нужны права администратора):
```bash
sudo mkdir -p /etc/nginx/sites-enabled/
```
Готово! Нам остается перезагрузить веб-сервер (нужны права администратора):
```bash
service nginx reload
```
И убедиться, что nginx работает корректно, набрав: ***_http://[ip_нашего_серера]_***. Должна отображаться страница: **`Welcome to nginx on Debian!`**