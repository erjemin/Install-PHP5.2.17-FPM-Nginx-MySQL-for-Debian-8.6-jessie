 # Установка сервера под старые проекты на базе PHP 5.2.17

Этот документ родился как плод усилий по восстановлению работоспособности старого сайта созданного на базе CMS Joomla! 1.0.15, требующего PHP 5.2.17. Возможно подобная задача встанет и перед вами, и тогда эта инструкция маожет помочь.

Установку сервера состояла из следующих мероприятий:

* Установка системы Debian (или аналога) и первичные настройки.
* Настройка SSH и сплеш-скрина.
* [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* Настройка Joomla! 1.0.15 и настройка phpMyAdmin и немного




apt-get mc
apt-get sudo
настраиваем sudoers
apt-get htop









$mosConfig_live_site = 'http://10.3.2.171';
$mosConfig_absolute_path = '/home/e-serg/intranet-old/html';
$mosConfig_cachepath = '/home/e-serg/intranet-old/html/cache';
$mosConfig_db = 'intranet';
$mosConfig_password = 'qwaseR12';
$mosConfig_user = 'e-serg';


время
sudo apt-get install ntp

конверер
sudo apt-get install alien

модуль найти
http://www.rpm-find.net/linux/rpm2html/search.php?query=php-ldap
скачать
wget ftp://ftp.pbone.net/mirror/yum.jasonlitka.com/EL5/x86_64/php-ldap-5.2.17-jason.2.x86_64.rpm
конвертнуть
sudo alien --to-deb php-ldap-5.2.17-jason.2.x86_64.rpm
некатить
sudo dpkg -i php-ldap_5.2.17-1_amd64.deb