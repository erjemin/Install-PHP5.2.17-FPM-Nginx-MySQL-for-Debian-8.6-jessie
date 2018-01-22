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

И поместим в него следующие коменды (не забываем меняьт **[db-user]** и **[secret_password_mysql_user]** на свои значения):

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
Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X`.

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


## Restore-скрипт

### Подготовительный этап

Для распковки архивов нам понадобится unzip. Установим его (нужны права администратора):

```bash
sudo apt-get install unzip
```

### Состав вкрипта restore

Нам потребуется найти самый свежий архив дампом каждой базы (если этих архивов несколько), распаковать каждый из них, восстановть базу в MySQL, незабыв удалить прометочный, распакованный, файл дампа. И з-атем проделать тодже самое, с файлами сайта. При этом нам достаточно распаковать только измененные файлы, но при этом защитить от изменений конфигурационный файл сайта -- `$HOME/[site]/html/configuration.php`.  Создадим скрипт в папке **[site]**:

```bash
nano ~/[site]/restore.sh
```

И поместим в него следующие команды (не забываем меняьт **[db-user]** и **[secret_password_mysql_user]** на свои значения):

```bash
echo "Восстановление базы из ДАМПа и АРХИВИРОВов!";
echo "│";
echo -ne "├─распаковываем дамп INTRANET: ";
if ls -d db-backup-intranet-*.sql.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  FileName=$(ls -tN db-backup-intranet-*.sql.zip | head -1);
  echo $FileName;
  then
    if [ -f "intranet.sql" ]; then rm intranet.sql; fi;
    unzip -q $FileName;
    if [ $? -ne 0 ];then echo "ОШИБКА: не удалось распаковать архив!"; exit 1; fi
  else  echo "ОШИБКА: нет файла архива!";  exit 1;
fi;
echo -ne "│ получен:			 ";
ls -sh --color=always intranet.sql;
echo -ne "│ посылаем дамп в MySQL:	 ";
mysql -u[db-user] -p[secret_password_mysql_user] intranet < intranet.sql
if [ $? -ne 0 ];
  then echo "ОШИБКА: бамп не всосался! Или сломана MySQL, или дамп, или произошло что-то непонятное!"; exit 1;
else echo "Ok!" ;fi;
rm intranet.sql;

echo "│";
echo -ne "├─распаковываем дамп PHONES2:	 ";
if ls -d db-backup-phones2--*.sql.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  FileName=$(ls -tN db-backup-phones2--*.sql.zip | head -1);
  echo $FileName;
  then
    if [ -f "phones2.sql" ]; then rm phones2.sql; fi;
    unzip -q $FileName;
    if [ $? -ne 0 ];then echo "ОШИБКА: не удалось распаковать архив!"; exit 1; fi
  else  echo "ОШИБКА: нет файла архива!";  exit 1;
fi;
echo -ne "│ получен:			 ";
ls -sh --color=always phones2.sql;
echo -ne "│ посылаем дамп в MySQL:	 ";
mysql -u[db-user] -p[secret_password_mysql_user] phones2 < phones2.sql
if [ $? -ne 0 ];
  then echo "ОШИБКА: бамп не всосался! Или сломана MySQL, или дамп, или произошло что-то непонятное!"; exit 1;
else echo "Ok!" ;fi
rm phones2.sql;

echo "│";
echo -ne "├─распаковываем дамп REESRT:	 ";
if ls -d db-backup-reestr---*.sql.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  FileName=$(ls -tN db-backup-reestr---*.sql.zip | head -1);
  echo $FileName;
  then
    if [ -f "reestr.sql" ]; then rm reeset.sql; fi;
    unzip -q $FileName;
    if [ $? -ne 0 ];then echo "ОШИБКА: не удалось распаковать архив!"; exit 1; fi
  else  echo "ОШИБКА: нет файла архива!";  exit 1;
fi;
echo -ne "│ получен:			 ";
ls -sh --color=always reestr.sql;
echo -ne "│ посылаем дамп в MySQL:	 ";
mysql -u[db-user] -p[secret_password_mysql_user] reestr < reestr.sql
if [ $? -ne 0 ];
   then echo "ОШИБКА: бамп не всосался! Или сломана MySQL, или дамп, или произошло что-то непонятное!"; exit 1;
else echo "Ok!" ;fi
rm reestr.sql;

echo "│";
echo -ne "├─целосность архива HTML:	 ";
if ls -d html-backup--------*.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  FileName=$(ls -tN html-backup--------*.zip | head -1);
  then
    unzip -t -qq $FileName;
    if [ $? -ne 0 ];
      then echo "ОШИБКА: нарушена целостность архива!"; exit 1;
      else
        echo "Ok!";
        echo "│";
        echo -ne "├─распаковываем архив HTML:	 ";
        unzip -u -o -C -qq $FileName -x html/configuration.php;
        if [ $? -ne 0 ];
          then echo "ОШИБКА: не удалось распаковать!";
        else echo "Ok!";
        fi;
    fi;
  else  echo "ОШИБКА: нет файла архива!";  exit 1;
fi;

echo "";
df -h
echo "";
echo ""
echo "      |\      _,,,---,,_"
echo "ZZZzz /,\`.-'\`'    -.  ;-;;,_"
echo "     |,4-  ) )-,_. ,\ (  \`'-'"
echo "    '---''(_/--'  \`-'\_)  "
echo ""
```
Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X`.

Испыттаем наш restore-скрипт. Переходим в папку с сайтом и запускам его:

```bash
cd [site]
bash restore.sh
```

В ответ получим (нужно подождать... работа с большими архивами дело не быстрое):

```txt
Восстановление базы из ДАМПа и АРХИВИРОВов!
│
├─распаковываем дамп INTRANET: db-backup-intranet-2018-01-22~16:43:51.sql.zip
│ получен:			 105M intranet.sql
│ посылаем дамп в MySQL:	 Ok!
│
├─распаковываем дамп PHONES2:	 db-backup-phones2--2018-01-22~16:44:06.sql.zip
│ получен:			 376K phones2.sql
│ посылаем дамп в MySQL:	 Ok!
│
├─распаковываем дамп REESRT:	 db-backup-reestr---2018-01-22~16:44:06.sql.zip
│ получен:			 55K reestr.sql
│ посылаем дамп в MySQL:	 Ok!
│
├─целосность архива HTML:	 Ok!
│
├─распаковываем архив HTML:	 Ok!
│
Файловая система Размер Использовано  Дост Использовано% Cмонтировано в
/dev/sda1           15G          13G  1,5G           91% /
udev                10M            0   10M            0% /dev
tmpfs               99M         4,4M   95M            5% /run
tmpfs              248M            0  248M            0% /dev/shm
tmpfs              5,0M            0  5,0M            0% /run/lock
tmpfs              248M            0  248M            0% /sys/fs/cgroup


      |\      _,,,---,,_
ZZZzz /,`.-'`'    -.  ;-;;,_
     |,4-  ) )-,_. ,\ (  `'-'
    '---''(_/--'  `-'\_)

```

Для проверки можем удалить отдельные файлы или папки из каталога `~/[site]/html` и уведиться, что после отработки нашего скрирта `restore.sh` эти папки и каталоги восстанавливаются.

Теперь нужно научить основной сервер отправлять backup-аривы на резервный севрер.


## Скрипт Send-Backup

### Подготовительный этап -- защищенная, шифрованная связь между серверами

Для того. чтобы мы могли пересылать с одного сервера на другой файты, и исполняьт команы на удаленном сервере не вводя каждый раз пароль, нужно настроить шифрованый ssh-канал между серверами.


