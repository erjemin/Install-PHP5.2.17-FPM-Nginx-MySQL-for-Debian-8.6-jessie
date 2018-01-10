### Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17

### Предыдущие этапы:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.

# 7: Настройка Joomla! 1.0.15

Для запуска нашего сайта нужно просто скопировать все файлы проекта в папку `home/[user]/[site]/html`.
Затем надо отредактировать файл 'configuration.php' в ней:

```bash
nano /home/[user]/[site]/html/configuration.php
```
Далее надо найти и заменить в ней следующие сроки:

```php
$mosConfig_live_site = 'http://[ip-нашего-сервера]/';
$mosConfig_absolute_path = '/home/[user]/[site]/html';
$mosConfig_cachepath = '/home/[user]/[site]/html/cache';
$mosConfig_user = '[db-user]';
$mosConfig_password = '[_secret_password_mysql_user_]';
$mosConfig_db = 'intranet';
```

Не забываем менять **[ip-нашего-сервера]**, **[user]**, **[site]**, **[db-user]** и **[_secret_password_mysql_user_]** на собсвнные значения.

Всё готово. Наш старый сайт должен работать и откликаться при вызове *http://[ip-нашего-сервера]*

--------------

## Следующие этапы:

* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) - доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).