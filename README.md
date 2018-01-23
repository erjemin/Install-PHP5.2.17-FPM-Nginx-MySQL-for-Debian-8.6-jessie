# Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17

Этот документ плод усилий по восстановлению работоспособности старого сайта, созданного на базе CMS Joomla! версии 1.0.15, требующего PHP 5.2.17. Возможно подобная задача встанет и перед вами, и тогда эта инструкция cможет помочь.

Установку сервера состояла из следующих этапов:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.
* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) — доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).
* 9. [Резервный сайт, резервное копирование и восстановление сайта и синхронизация](backup-restore-and-syncronization-sites.md).









