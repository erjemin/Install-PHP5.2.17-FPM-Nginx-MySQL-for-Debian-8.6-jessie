### Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17

### Предыдущие этапы:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.

# 7: Настройка Joomla! 1.0.15

Для запуска нашего сайта нужно просто скопировать все файлы проекта в папку `home/[user]/[site]/html`. Получить файлы можно сразу с другого сервера (ключи `-rp` сообщают, что копирование фалов надо произвести рекурсивно с сохранением даты и времени их создания):

```bash
scp -rp [user-for-old-hos]@[old-host]:/var/www/html/*.* home/[user]/[site]/html
```

Затем следует отредактировать файл 'configuration.php' с конфигурацией Joomal:

```bash
nano /home/[user]/[site]/html/configuration.php
```

Для это надо найти и заменить в ней следующие сроки:

```php
$mosConfig_live_site = 'http://[ip-нашего-сервера]/';
$mosConfig_absolute_path = '/home/[user]/[site]/html';
$mosConfig_cachepath = '/home/[user]/[site]/html/cache';
$mosConfig_user = '[db-user]';
$mosConfig_password = '[_secret_password_mysql_user_]';
```

Не забываем менять **[ip-нашего-сервера]**, **[user]**, **[site]**, **[db-user]** и **[_secret_password_mysql_user_]** на собсвнные значения.

Аналогичные изменения нужно сделать и в настройках `config.php` телефонного справочника `~/[home]/[site]/html/spravochnic/`

```bash
nano /home/[user]/[site]/html/spravochnic/config.php
```

В нем тоже нужно именить значения **[db-user]** и **[_secret_password_mysql_user_]**:

```php
define('DB_USER', '[db-user]);
define('DB_PASSWORD', '[_secret_password_mysql_user_]');
```

Всё готово. Наш старый сайт должен работать и откликаться при вызове *http://[ip-нашего-сервера]*

--------------

## Следующие этапы:

* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) - доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).
* 9. [Резервный сайт, резервное копирование и восстановление сайта и синхронизация](backup-restore-and-syncronization-sites.md).