### Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17

### Предыдущие этапы:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.
* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) — доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).

# 9: Резервный сайт, резервное копирование и восстановление сайта и сихронизация.

## Создаем разервный сервер

Развертывание резервного сервера ничем не отличается от развертывания основного. Рекомендуется использвать теже имена пользователя системы -- **[user]**, пароль пользователя -- **[user_pwd]**, имя пользователя базы данных MySQL -- **[db-user]** и его пароля **[secret_password_mysql_user]** и папку для расположения сайта -- **[site]**.

При этом надо учитывать, что у резервного сайта будет другой **[ip-нашего-сервера]** (назовём его **[ip-рерзвного-сервера]**) и также имя хоста. И это надо учесть в конфигурационном файле nginx `$HOME/[site]/config/[site]_nginx.conf` (см. [сответствующий раздер в главе «Установка и настройка веб-сервера nginx»](install-and-adjust-nginx-f%D0%BEr-php-5.2.17.md#%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-nginx-%D0%B4%D0%BB%D1%8F-%D0%BD%D0%B0%D1%88%D0%B5%D0%B3%D0%BE-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B0)) и Joomla `$HOME/[site]/html/configuration.php` (см. см. [сответствующий раздер в главе «Настройка Joomla! 1.0.15»](settings-config-for-Joolma-1.0.15.md#7-%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-joomla-1015)).

Если наш сервер работает как виртуальная машина, будет проще просто её кланоировать. Но при этом надо учесть, что у «клона» основого сервера нужно изменить имя компьютера и хоста. Имя компютера находится в файле `/etc/hostname`, а хоста в `/etc/hosts`. Для их редактирования понадятся права администратора. Изменения настроек подействуют после презагрузки.

## Backup-скрипт

### Подготовительный этап

Для создания архивов нам понадобится zip. Установим его (нужны права администратора):

```bash
sudo apt-get install zip
```

### Состав вкрипта backup

Нам потребуется получить дамп всех баз из MySQL, сделать архивы с меткой времени и заархивипровать файлы сайта. Создадим скрпит   в папке **[site]**:

```bash
nano ~/[site]/backup.sh
```

И поместим в него следующие коменды:

```bash
echo "ДАМП и АРХИВИРОВАНИЕ БАЗЫ!";

if ls -d db-backup-intranet-*.sql.zip >/dev/null 2>&1;
  then rm db-backup-intranet-*.sql.zip -f;
fi;
echo -ne ">> база INTRANET >>";
mysqldump -u[db-user] -p[secret_password_mysql_user] intranet > intranet.sql
zip db-backup-intranet-$(date +%Y-%m-%d~%H:%M:%S).sql.zip intranet.sql -9;
rm intranet.sql -f;
echo -ne ">> готово        >>  ";
ls -sh --color=always db-backup-intranet-*.sql.zip;

if ls -d db-backup-phones2--*.sql.zip >/dev/null 2>&1;
  then rm db-backup-phones2--*.sql.zip -f;
fi;
echo -ne ">> база PHONES2  >>";
mysqldump -u[db-user] -p[secret_password_mysql_user] phones2 > phones2.sql
zip db-backup-phones2--$(date +%Y-%m-%d~%H:%M:%S).sql.zip phones2.sql -9;
rm phones2.sql -r;
echo -ne ">> готово        >>  ";
ls -sh --color=always db-backup-phones2--*.sql.zip;
echo -ne ">> база REESTR   >>";

if ls -d db-backup-reestr---*.sql.zip >/dev/null 2>&1;
  then rm db-backup-reestr---*.sql.zip -f;
fi;
mysqldump -u[db-user] -p[secret_password_mysql_user] reestr > reestr.sql
zip db-backup-reestr---$(date +%Y-%m-%d~%H:%M:%S).sql.zip reestr.sql -9; 
rm reestr.sql -f;
echo -ne ">> готово        >>  ";
ls -sh --color=always db-backup-reestr--*.sql.zip;

echo ""
echo "АРХИВИРОВАНИЕ КАТАЛОГА HTML";
if ls -d html-backup--------*.zip >/dev/null 2>&1;
  then rm html-backup--------*.zip -f;
fi;
zip html-backup--------$(date +%Y-%m-%d~%H:%M:%S).zip html -r9 -qq; 
echo -ne ">> готово        >>  ";
ls -sh --color=always html-backup-*.zip;
echo ""
df -h
echo "";
echo " _._     _,-'\"\"\`-._";
echo "(,-.\`._,'(       |\\\`-/|";
echo "    \`-.-' \\ )-\`( , o o)";
echo " УРА!     \`-    \\\`_\`\"'-  Всё получилось! Вот вам котик за старания!";
echo "";
```

Испыттаем наш скрипт. Переходим в папку с сайтом и запускам его:

```bash
cd [site]
bash backup.sh
```

В ответ получим (нужно подождать... работа с большими архивами дело не быстрое):

```txt
ДАМП и АРХИВИРОВАНИЕ БАЗЫ!
>> база INTRANET >>  adding: intranet.sql (deflated 80%)
>> готово        >>  22M db-backup-intranet-2018-01-22~16:43:51.sql.zip
>> база PHONES2  >>  adding: phones2.sql (deflated 84%)
>> готово        >>  64K db-backup-phones2--2018-01-22~16:44:06.sql.zip
>> база REESTR   >>  adding: reestr.sql (deflated 66%)
>> готово        >>  20K db-backup-reestr---2018-01-22~16:44:06.sql.zip

АРХИВИРОВАНИЕ КАТАЛОГА HTML
>> готово        >>  5,0G html-backup--------2018-01-22~16:44:06.zip

Файловая система Размер Использовано  Дост Использовано% Cмонтировано в
/dev/sda1           15G          13G  1,5G           91% /
udev                10M            0   10M            0% /dev
tmpfs               99M         4,4M   95M            5% /run
tmpfs              248M            0  248M            0% /dev/shm
tmpfs              5,0M            0  5,0M            0% /run/lock
tmpfs              248M            0  248M            0% /sys/fs/cgroup

 _._     _,-'""`-._
(,-.`._,'(       |\`-/|
    `-.-' \ )-`( , o o)
 УРА!     `-    \`_`"'-  Всё получилось! Вот вам котик за старания!


```

Проверим, что скрипт отработал и в папке появились архивы:

```bash
ls -sh1t
```

Получим примерно такой ответ:

```txt
5,0G html-backup--------2018-01-22~16:44:06.zip
 20K db-backup-reestr---2018-01-22~16:44:06.sql.zip
 64K db-backup-phones2--2018-01-22~16:44:06.sql.zip
 22M db-backup-intranet-2018-01-22~16:43:51.sql.zip
8,0K send-backup.sh
4,0K socket
4,0K backup.sh
4,0K html
4,0K log
4,0K config
```

Скрипт работает и резервные копии создаются.

Теперь нужно научиться восстанавливать сайт из этой резервной копии.

## Backup-скрипт

### Подготовительный этап

Для создания архивов нам понадобится zip. Установим его (нужны права администратора):

```bash
sudo apt-get install zip
```

### Состав вкрипта backup

Нам потребуется получить дамп всех баз из MySQL, сделать архивы с меткой времени и заархивипровать файлы сайта. Создадим скрпит   в папке **[site]**:

```bash
nano ~/[site]/backup.sh
```

И поместим в него следующие коменды:

```bash
echo "ДАМП и АРХИВИРОВАНИЕ БАЗЫ!";