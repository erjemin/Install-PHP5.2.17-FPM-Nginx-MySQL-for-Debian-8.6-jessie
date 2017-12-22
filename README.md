 # Установка сервера под старые проекты на базе PHP 5.2.17

Этот документ родился как плод усилий по восстановлению работоспособности старого сайта созданного на базе CMS Joomla! 1.0.15, требующего PHP 5.2.17. Возможно подобная задача встанет и перед вами, и тогда эта инструкция маожет помочь.

Установку сервера состояла из следующих мероприятий:

* [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* Настройка SSH и сплеш-скрина.
* [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* Настройка Joomla! 1.0.15 и настройка phpMyAdmin и немного
* Доустанавливаем поддержку LDAP (доступ к Active Directory)




apt-get mc
apt-get htop









$mosConfig_live_site = 'http://10.3.2.171';
$mosConfig_absolute_path = '/home/e-serg/intranet-old/html';
$mosConfig_cachepath = '/home/e-serg/intranet-old/html/cache';
$mosConfig_db = 'intranet';
$mosConfig_password = 'qwaseR12';
$mosConfig_user = 'e-serg';


время
sudo apt-get install ntp

-------------------
Обеспечение работы LDP и проверка: https://wiki.it-kb.ru/unix-linux/linux-cli-tools/openldap-ldap-client-check-connection-to-active-directory-domain-controller-with-ldapsearch
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

-----------



конверер пакетов
sudo apt-get install alien
модуль найти
http://www.rpm-find.net/linux/rpm2html/search.php?query=php-ldap
скачать
wget ftp://ftp.pbone.net/mirror/yum.jasonlitka.com/EL5/x86_64/php-ldap-5.2.17-jason.2.x86_64.rpm
конвертнуть
sudo alien --to-deb php-ldap-5.2.17-jason.2.x86_64.rpm
некатить
sudo dpkg -i php-ldap_5.2.17-1_amd64.deb

получаем git
sudo apt-get install git


sudo dpkg -l | grep nginx



