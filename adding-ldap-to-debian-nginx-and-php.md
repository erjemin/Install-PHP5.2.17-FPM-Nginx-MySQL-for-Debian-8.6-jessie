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
Чтобы добавить динамические модули к текущей установке nginx, нам потребуется узнать — с какими параметрами он был собран. Если собирать с параметрами, в которых будут только новые модули, nginx будет ругаться и не позволит использовать такой модуль.
Для того, чтобы узнать, с какими параметрами был установлен nginx (в том числе, из репозитория), нужно набрать команду (нужны права администратора):
```bash
sudo nginx -V
```
Она сообщит, все ключи, которые были испольованы при сборке (не только для собранных в ручную версий, но и для версии <<прилетающей>> в систему из репозиториев. Сообщение выглядит примерно так:
>
``` nginx version: nginx/1.12.2
built by gcc 4.9.2 (Debian 4.9.2-10)
built with OpenSSL 1.0.1t  3 May 2016

> TLS SNI support enabled`

> configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie'
```