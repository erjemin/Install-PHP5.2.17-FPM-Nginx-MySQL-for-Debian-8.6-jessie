 # Установка сервера под старые проекты на базе PHP 5.2.17

Этот документ родился как плод усилий по восстановлению работоспособности старого сайта созданного на базе CMS Joomla! 1.0.15, требующего PHP 5.2.17. Возможно подобная задача встанет и перед вами, и тогда эта инструкция маожет помочь.

Установку сервера состояла из следующих мероприятий:

* Установка системы Debian (или аналога) и первичные настройки.
* Настройка SSH и сплеш-скрина.
* [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* Установка и настройка phpMyAdmin и немного настроек Joomla! 1.0.15.




apt-get mc
apt-get sudo
настраиваем sudoers
apt-get htop




mysql -u root -p secret_password_mysql_root

CREATE DATABASE intranet DEFAULT CHARACTER SET cp1251 DEFAULT COLLATE cp1251_general_ci;

CREATE DATABASE phones2 DEFAULT CHARACTER SET latin1 DEFAULT COLLATE latin1_swedish_ci;

Затем, чтобы наше Django-приложение не работало с базой под аккаунтом супер-пользователя root, создаем нового пользователя базы [user] с паролем «secret_password_mysql_user»:
GRANT ALL PRIVILEGES ON intranet.* TO 'e-serg'@'localhost' IDENTIFIED BY 'qwaseR12';
GRANT ALL PRIVILEGES ON phones2.* TO 'e-serg'@'localhost' IDENTIFIED BY 'qwaseR12';

SHOW VARIABLES LIKE '%time_zone%';

quit;




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

модуль
wget ftp://ftp.pbone.net/mirror/yum.jasonlitka.com/EL5/x86_64/php-ldap-5.2.17-jason.2.x86_64.rpm

sudo alien --to-deb php-ldap-5.2.17-jason.2.x86_64.rpm

sudo dpkg -i php-ldap_5.2.17-1_amd64.deb