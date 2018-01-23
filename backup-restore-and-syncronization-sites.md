### Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17
### Предыдущие этапы:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).
* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.
* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) - доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).

# 9: Резервный сайт, резервное копирование и восстановление сайта и синхронизация.

## Создаём резервный сервер

Развёртывание резервного сервера ничем не отличается от развёртывания основного. Рекомендуется использовать те же имена пользователя системы - **[user]**, пароль пользователя - **[user_pwd]**, имя пользователя базы данных MySQL - **[db-user]** и его пароля **[secret_password_mysql_user]** и папку для расположения сайта - **[site]**.

При этом надо учитывать, что у резервного сайта будет другой **[ip-нашего-сервера]** (назовём его **[ip-резервного-сервера]**) и также имя хоста. И это надо учесть в конфигурационном файле nginx `$HOME/[site]/config/[site]_nginx.conf` (см. [соответствующий раздел в главе «Установка и настройка веб-сервера nginx»](install-and-adjust-nginx-f%D0%BEr-php-5.2.17.md#%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-nginx-%D0%B4%D0%BB%D1%8F-%D0%BD%D0%B0%D1%88%D0%B5%D0%B3%D0%BE-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B0)) и Joomla `$HOME/[site]/html/configuration.php` (см. см. [соответствующий раздел в главе «Настройка Joomla! 1.0.15»](settings-config-for-Joolma-1.0.15.md#7-%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-joomla-1015)).

Если наш сервер работает как виртуальная машина, будет проще просто её клонировать. Но при этом надо учесть, что у «клона» осинового сервера нужно изменить имя компьютера и хоста. Имя компьютера находится в файле `/etc/hostname`, а хоста в `/etc/hosts`. Для их редактирования понадобятся права администратора. Изменения настроек подействуют после перезагрузки.

## Backup-скрипт

### Подготовительный этап

Для создания архивов нам понадобится zip. Установим его (нужны права администратора):

```bash
sudo apt-get install zip
```

### Скрипт backup

Нам потребуется получить дамп всех баз из MySQL, сделать архивы с меткой времени и заархивировать файлы сайта. Создадим скрипт в папке **[site]**:

```bash
nano ~/[site]/backup.sh
```

И поместим в него следующие команды (не забываем менять **[db-user]** и **[secret_password_mysql_user]** на свои значения):

```bash
echo "ДАМП и АРХИВИРОВАНИЕ БАЗЫ!";
cd /home/[user]/WEB
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
Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X` и испытаем наш скрипт:

```bash
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

Для распаковки архивов нам понадобится unzip. Установим его (нужны права администратора):

```bash
sudo apt-get install unzip
```

### Состав вкрипта restore

Нам потребуется найти самый свежий архив дампом каждой базы (если этих архивов несколько), распаковать каждый из них, восстановить базу в MySQL, не забыв удалить промежуточный, распакованный, файл дампа. И затем проделать то же самое, с файлами сайта. При этом нам достаточно распаковать только изменённые файлы, но при этом защитить от изменений конфигурационный файл сайта - `$HOME/[site]/html/configuration.php`.  Создадим скрипт в папке **[site]**:

```bash
nano ~/[site]/restore.sh
```

И поместим в него следующие команды (не забываем менять **[db-user]** и **[secret_password_mysql_user]** на свои значения):

```bash
echo "Восстановление базы из ДАМПа и АРХИВов!";
cd /home/[user]/WEB
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
echo -ne "├─целостность архива HTML:	 ";
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
echo "      |\      _,,,---,,_";
echo "ZZZzz /,\`.-'\`'    -.  ;-;;,_   Восстановление прошло успешно";
echo -ne "     |,4-  ) )-,_. ,\ (  \`'-'  ";
date;
echo "    '---''(_/--'  \`-'\_)  ";
echo "";
```
Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X`.

Испытаем наш restore-скрипт. Запускаем его:

```bash
bash restore.sh
```

В ответ получим (нужно подождать... работа с большими архивами дело не быстрое):

```txt
Восстановление базы из ДАМПа и АРХИВов!
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
├─целостность архива HTML:	 Ok!
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
ZZZzz /,`.-'`'    -.  ;-;;,_   Восстановление прошло успешно
     |,4-  ) )-,_. ,\ (  `'-'  Пн янв 22 17:45:25 MSK 2018
    '---''(_/--'  `-'\_)

```

Для проверки можем удалить отдельные файлы или папки из каталога `~/[site]/html` и убедиться, что после отработки нашего скрипта `restore.sh` эти папки и каталоги восстанавливаются.

Теперь нужно научить основной сервер отправлять backup-аривы на резервный сервер.


## Скрипт Send-Backup

### Подготовительный этап - защищённая связь между серверами

Для того. чтобы мы могли пересылать файлы с одного сервера на другой, и исполнять команды на удалённом сервере и при этом не вводить каждый раз пароль, нужно настроить шифрованный ssh-канал между серверами. Это позволит нам запускать копирование в режиме bash-скрипта.

Мы будем производить копирование только с основного сервера на резервный. Поэтому все последующие настройки выполняются на основном сервере. Если вы хотите, чтобы и резервный сервер мог оправлять файлы на основной, вам надо проделать всё тоже самоё и на нём.

#### Создаём SSH-ключи

Генерация пары публичный/приватный ключей производим следующей командой:
```bash
ssh-keygen -t rsa
```

В процессе создания ключей будет запрошены где разместить ключи (по умолчанию в папке `/home/[user]/.ssh/id_rsa`) и пароль для доступа к ключам. Просто нажмём `Enter`:

```txt
Generating public/private rsa key pair.
Enter file in which to save the key (/home/[user]/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/[user]/.ssh/id_rsa.
Your public key has been saved in /home/[user]/.ssh/id_rsa.pub.
The key fingerprint is:
1e:89:cf:95:5b:1f:a8:dc:48:ae:e4:2a:67:d4:4c:54 [user]@[host]
The key's randomart image is:
+---[RSA 2048]----+
|         .E      |
|        .        |
|       .+        |
|       ..        |
|  =   .+S        |
|     ...oo       |
|     .. + o o .  |
|      ++ = = *   |
|     o .=.o.* .  |
+-----------------+
```

Теперь можно проверить, что ключи действительно созданы и находятся в каталоге `~/.ssh`:

```bash
ls -l ~/.ssh
```

получим, что-то типа:
```
итого 12
-rw------- 1 [user] [user] 1679 янв 23 10:53 id_rsa
-rw-r--r-- 1 [user] [user]  397 янв 23 10:53 id_rsa.pub
-rw-r--r-- 1 [user] [user]  664 янв 11 14:33 known_hosts
```

#### Добавляем локальный ключ авторизации

Наш приватный ключ остаётся на нашем компьютере, но ssh-агенту нужно знать где он находится. Сообщим ему расположение ключа:

```bash
ssh-add ~/.ssh/id_rsa
```

Если команда не проходит и в ответ вы получаете `Could not open a connection to your authentication agent`, то значит `ssh-agent` не стартовал и нужно это сделать:

```ssh
eval `ssh-agent`
```

и повторить `ssh-add`:

 ```bash
ssh-add ~/.ssh/id_rsa
```

В ответ должны получить сообщение, что приватный ключ добавлен:

```txt
Identity added: /home/e-serg/.ssh/id_rsa (rsa w/o comment)
```

#### Отправляем публичный ключ на резервный сервер

Теперь нам надо чтобы резервный сервер получил публичный ключ `id_rsa.pub` с нашего компьютера. Перешлём его:

```bash
scp ~/.ssh/id_rsa.pub [user]@[ip-резервного-сервера]:~/.ssh/
```

Аутентификация запросит подтверждение на соединение и занесение удалённого компьютера в список доверия:

```txt
The authenticity of host '[ip-резервного-сервера] ([ip-резервного-сервера])' can't be established.
ECDSA key fingerprint is e5:1f:de:7d:45:9b:97:c9:09:ca:e2:d9:4c:08:04:20.
Are you sure you want to continue connecting (yes/no)?
```

Отвечаем `yes` и жмём 'Enter'. После система запросим пароль для пользователя **[user]** на резервном сервере:

```txt
Warning: Permanently added '[ip-резервного-сервера]' (ECDSA) to the list of known hosts.
[user]@[ip-резервного-сервера]'s password:
```

Вводим его, копирование публичного ключа завершится:

```txt
 id_rsa.pub                                                        100%  397     0.4KB/s   00:00
```

#### Прописываем публичный ключ в авторизованные (authorized_keys) на резервном сервере.

Теперь нам осталось добавить скопированный с нашего компьютера публичный ключ в файл `authorized_keys` на резервном сервере (удалённом компьютере). Это можно сделать непосредственно на нём, но можно сделать и удалённо через SSH (будет запрошен пароль пользователя **[user]** на удалённом компьютере **[ip-резервного-сервера]**, не забудьте поменять эти значения):

```bash
ssh [user]@[ip-резервного-сервера] 'cat ~/.ssh/id_rsa.pub  >> ~/.ssh/authorized_keys'
```

Для дополнительной безопасности можно изменить разрешения на файл `authorized_keys` на ударленного резервного сервера на «только чтение/запись». Сделаем это, то же, через SSH:

```bash
ssh [user]@[ip-резервного-сервера] 'chmod 600 ~/.ssh/authorized_keys'
```

Обратите внимание, эта команда уже будет выполнена без запроса логина и пароля. Выполним ещё несколько команд для проверки:

```bash
 ssh [user]@[ip-резервного-сервера] 'ls -l ~/.ssh'
```

В ответ получим список файлов ключей на резервном сервере:
```txt
итого 12
-rw------- 1 [user] [user] 793 янв 23 12:15 authorized_keys
-rw-r--r-- 1 [user] [user] 397 янв 23 11:53 id_rsa.pub
-rw-r--r-- 1 [user] [user] 222 дек 27 16:39 known_hosts
```

### Состав вкрипта send-backup

Нам потребуется найти самый свежий архив дампом каждой базы (если этих архивов несколько), удалённо проверить есть ли архивы с такими дампами на резервном сервере, и удалить их, если они есть, после каждый файл архива скопировать на резервный сервер.  Создадим скрипт в папке **[site]**:

```bash
nano ~/[site]/send-backup.sh
```

И поместим в него следующие команды (не забываем меняьт **[user]** и **[ip-резервного-сервера]** на свои значения):

```bash
echo "Отправляем бекапы на РЕЗЕРВНЫЙ СЕРВЕР ([ip-резервного-сервера])!";
cd /home/[user]/WEB
echo "│";
echo -ne "├─архив дампа INTRANET:	 ";
if ls -d db-backup-intranet-*.sql.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  then FileName=$(ls -tN db-backup-intranet-*.sql.zip | head -1);
  echo $FileName;
  if ssh [user]@[ip-резервного-сервера] 'ls -d ~/WEB/db-backup-intranet-*.sql.zip >/dev/null 2>&1';
    then
      echo -ne "│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	";
      ssh [user]@[ip-резервного-сервера] 'rm ~/WEB/db-backup-intranet-*.sql.zip -f';
      if [ $? -ne 0 ];
        then echo "ОШИБКА: не удалось удалить файлы db-backup-intranet-*.sql.zip на [ip-резервного-сервера]"; exit 1;
      else
        echo " Ok!";
      fi
  else
    echo "│ старый файл  на [ip-резервного-сервера] (intranet2) отсутсвует";
  fi;
  echo -ne "│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	"
  scp -pq ~/WEB/$FileName [user]@[ip-резервного-сервера]:~/WEB/
  if [ $? -ne 0 ];
    then echo "ОШИБКА: не удалось скопировать архив на [ip-резервного-сервера]"; exit 1;
  else 
    echo " Ok!";
  fi
else
  echo "│ ОШИБКА: нет исходного файла архива для копирования!";
fi;

echo "│";
echo -ne "├─архив дампа PHONES2:	 ";
if ls -d db-backup-phones2--*.sql.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  then FileName=$(ls -tN db-backup-phones2--*.sql.zip | head -1);
  echo $FileName;
  if ssh [user]@[ip-резервного-сервера] 'ls -d ~/WEB/db-backup-phones2--*.sql.zip >/dev/null 2>&1';
    then
      echo -ne "│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	";
      ssh [user]@[ip-резервного-сервера] 'rm ~/WEB/db-backup-phones2--*.sql.zip -f';
      if [ $? -ne 0 ];
        then echo "ОШИБКА: не удалось удалить файлы db-backup-phones2--*.sql.zip на [ip-резервного-сервера]"; exit 1;
      else
        echo " Ok!";
      fi
  else
    echo "│ старый файл  на [ip-резервного-сервера] (intranet2) отсутсвует";
  fi;
  echo -ne "│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	"
  scp -pq ~/WEB/$FileName [user]@[ip-резервного-сервера]:~/WEB/
  if [ $? -ne 0 ];
    then echo "ОШИБКА: не удалось скопировать архив на [ip-резервного-сервера]"; exit 1;
  else 
    echo " Ok!";
  fi
else
  echo "│ ОШИБКА: нет исходного файла архива для копирования!";
fi;

echo "│";
echo -ne "├─архив дампа REESTR:	 ";
if ls -d db-backup-reestr---*.sql.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  then FileName=$(ls -tN db-backup-reestr---*.sql.zip | head -1);
  echo $FileName;
  if ssh [user]@[ip-резервного-сервера] 'ls -d ~/WEB/db-backup-reestr---*.sql.zip >/dev/null 2>&1';
    then
      echo -ne "│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	";
      ssh [user]@[ip-резервного-сервера] 'rm ~/WEB/db-backup-reestr---*.sql.zip -f';
      if [ $? -ne 0 ];
        then echo "ОШИБКА: не удалось удалить файлы db-backup-reestr---*.sql.zip на [ip-резервного-сервера]"; exit 1;
      else
        echo " Ok!";
      fi
  else
    echo "│ старый файл  на [ip-резервного-сервера] (intranet2) отсутсвует";
  fi;
  echo -ne "│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	"
  scp -pq ~/WEB/$FileName [user]@[ip-резервного-сервера]:~/WEB/
  if [ $? -ne 0 ];
    then echo "ОШИБКА: не удалось скопировать архив на [ip-резервного-сервера]"; exit 1;
  else 
    echo " Ok!";
  fi
else
  echo "│ ОШИБКА: нет исходного файла архива для копирования!";
fi;

echo "│";
echo -ne "├─архив HTML:	 ";
if ls -d html-backup--------*.zip >/dev/null 2>&1;
  # получаем самый свежий файл из множества
  then FileName=$(ls -tN html-backup--------*.zip | head -1);
  echo $FileName;
  if ssh [user]@[ip-резервного-сервера] 'ls -d ~/WEB/html-backup--------*.zip >/dev/null 2>&1';
    then
      echo -ne "│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	";
      ssh [user]@[ip-резервного-сервера] 'rm ~/WEB/html-backup--------*.zip -f';
      if [ $? -ne 0 ];
        then echo "ОШИБКА: не удалось удалить файлы html-backup--------*.zip на [ip-резервного-сервера]"; exit 1;
      else
        echo " Ok!";
      fi
  else
    echo "│ старый файл  на [ip-резервного-сервера] (intranet2) отсутствует";
  fi;
  echo -ne "│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	"
  scp -pq ~/WEB/$FileName [user]@[ip-резервного-сервера]:~/WEB/
  if [ $? -ne 0 ];
    then echo "ОШИБКА: не удалось скопировать архив на [ip-резервного-сервера]"; exit 1;
  else 
    echo " Ok!";
  fi
else
  echo "│ ОШИБКА: нет исходного файла архива для копирования!";
fi;

echo "│";
echo "│ Система на этом компьютере:";
echo "│";
df -h
echo "│";
echo "│ Система на [ip-резервного-сервера] (intranet2):";
echo "│";
      ssh [user]@[ip-резервного-сервера] 'df -h';
echo "  ,-.       _,---._ __  / \\";
echo " /  )    .-'       \`./ /   \\";
echo "(  (   ,'            \`/    /|";
echo " \  \`-\"             \'\   / |";
echo "  \`.              ,  \ \ /  |";
echo "   /\`.          ,'-\`----Y   |";
echo "  (            ;        |   '";
echo "  |  ,-.    ,-'         |  / ";
echo -ne "  |  | (   | СКОПИРОВАНО| /  ";
date;
echo "  )  |  \  \`.___________|/";
echo "  \`--'   \`--'";
```
Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X`. Теперь испытаем наш скрипт:

```bash
bash send-backup.sh
```

В ответ получим (нужно подождать... работа с большими архивами дело не быстрое):

```txt
Отправляем бекапы на РЕЗЕРВНЫЙ СЕРВЕР ([ip-резервного-сервера])!
│
├─архив дампа INTRANET:	 db-backup-intranet-2018-01-23~12:48:53.sql.zip
│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	 Ok!
│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	 Ok!
│
├─архив дампа PHONES2:	 db-backup-phones2--2018-01-23~12:49:18.sql.zip
│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	 Ok!
│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	 Ok!
│
├─архив дампа REESTR:	 db-backup-reestr---2018-01-23~12:49:19.sql.zip
│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	 Ok!
│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	 Ok!
│
├─архив HTML:	 html-backup--------2018-01-23~12:49:19.zip
│ удаляем старые архивы на [ip-резервного-сервера] (intranet2):	 Ok!
│ копируем свежий архив на [ip-резервного-сервера] (intranet2):	 Ok!
│
│ Система на этом компьютере:
│
Файловая система Размер Использовано  Дост Использовано% Cмонтировано в
/dev/sda1           15G          13G  1,3G           92% /
udev                10M            0   10M            0% /dev
tmpfs              401M         5,4M  396M            2% /run
tmpfs             1002M            0 1002M            0% /dev/shm
tmpfs              5,0M            0  5,0M            0% /run/lock
tmpfs             1002M            0 1002M            0% /sys/fs/cgroup
│
│ Система на [ip-резервного-сервера] (intranet2):
│
Файловая система Размер Использовано  Дост Использовано% Cмонтировано в
/dev/sda1           15G          13G  1,4G           91% /
udev                10M            0   10M            0% /dev
tmpfs              401M         5,4M  396M            2% /run
tmpfs             1002M            0 1002M            0% /dev/shm
tmpfs              5,0M            0  5,0M            0% /run/lock
tmpfs             1002M            0 1002M            0% /sys/fs/cgroup
  ,-.       _,---._ __  / \
 /  )    .-'       `./ /   \
(  (   ,'            `/    /|
 \  `-"             \'\   / |
  `.              ,  \ \ /  |
   /`.          ,'-`----Y   |
  (            ;        |   '
  |  ,-.    ,-'         |  / 
  |  | (   | СКОПИРОВАНО| /  Вт янв 23 13:04:18 MSK 2018
  )  |  \  `.___________|/
  `--'   `--'
```

Всё. Автоматической распаковки на резервном сервере не требуется и будет производится вручную скриптом `restore.sh`. Осталось написать скрипт который чистит резервный сервер от лишних файлов, удалённых на основном сервере.


## Автоматический запуск криптов по расписанию

Мы будем ежедневно запускать `backup.sh` и `send-backup.sh` на основном сервере. Команда:

```bash
crontab -e
```

откроет файл управляющий расписанием. Добавим в него следующие строки:

```txt
15 19 * * 1-5 [user] /home/[user]/[site]/backup.sh
45 19 * * 1-5 [user] /home/[user]/[site]/send-backup.sh
```

Это означает, что каждый рабочий день (c понедельника по пятницу) в 19:15 будет запучен скрпит `backup.sh`, а в 19:45 запускается `send-backup.sh`.

Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X`. Теперь запуск по расписанию работает.