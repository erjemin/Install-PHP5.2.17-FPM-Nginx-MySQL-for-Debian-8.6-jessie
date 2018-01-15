### Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17

### Предыдущие этапы:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.
* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) — доступ к Active Directory.

# 8: Чистка системы от мусора

В процессе сборки PHP нам пришлось установить кучу пакетов разработчика, зависимых пакетов, скачать архивы и сорать объектные файлы. Чтобы не захламлять севрвер можно почистить систему и освободить диск от ненужных файлов.

Удаляем файлы с исходными кодами пакетов и модулей из домашней папки:
```bash
rm $HOME/nginx_signing.key
rm $HOME/php-5.2.17-libxml2.patch
rm $HOME/php-5.2.17-openssl.patch
rm $HOME/php-5.2.17.tar.gz
rm $HOME/php-fpm.diff.gz
rm -r $HOME/php-5.2.17
```

# ВСЁ
# КОНЕЦ

-----------------------

### *Для самых пытливых:*

Можно попробовать деинсталлировать ненужные пакеты разработчика. У нас были усьтановлены следующие модули: `libxml2-dev`, `libmysqlclient-dev`, `libcurl4-gnutls-dev`, `libpng12-dev`, `libjpeg62-turbo-dev`, `libjpeg-dev`, `libxslt1-dev`, `libbz2-dev`, `libmcrypt-dev`, `libmhash-dev`, `libfcgi-dev`, `libmhash-dev`, `libssl-dev`, `libcrypto++-dev`, `lib32z1-dev`, `libltdl-dev`, `libcurl4-openssl-dev`, `libexpat1-dev`, `libsasl2-dev`, `python-dev`, `libldap2-dev`, `libssl-dev` и `libpcre3-dev`

Для деинсталляции требуются права администратора:

```bash
sudo apt-get purge имя-пакета
```

Перед удалением паетов рекомендуется делать резервную копию системы. Удаленный пакет не всегда удается корректно вернуть в систему. Пакеты рекомендуются удалять по одному. После каждого удаления тщательно тестировать и проверять работоспособность всех функций. После удаления каждого пакета чистим неиспользуемые («подвисшие») и зависимые пакеты, которые не удалилось деинсталлировать с помощью предыдущей команды (требуются права администратора):

```bash
sudo apt-get autoremove
```

