# Установка и настройка сервера MySQL

MySQL – популярная система управления базами данных (СУБД), которая используется для хранения и извлечения данных множества различных приложений. Установить ее в систему можно одной командой `sudo apt-get install mysql-server`. Но если нужно установить самую свежую версию версию (или версию с поддержкой кластеров) нужно сначала добавить репозитоии MySQL, и с помощью стандартного пакетного менеджера системы получать свежие версии и обновления сразу оттуда.

## Добавление репозитория MySQL

Разработчики MySQL поддерживают несколько веток сервера, и для удобства подключения правильных репозиториев предоставляют установочный файл .deb, который отвечает за их настройку и установку. Загрузим и установим настройщик репозиториев.

[Открываем страницу загрузок сайта MySQL в браузере](https://dev.mysql.com/downloads/repo/apt/). Найдем кнопку **Download** в правом нижнем углу и перейдем на стнанмцу загрузок. Мосжно пропустить регистрацию, найти ссылку **No thanks, just start my download**. Щелкнем правой кнопкой мыши по ссылке и скопируем ссылку.

Скачиваем установочный файл в нашу домашнюю директорию:
```bash
cd ~
wget https://dev.mysql.com/get/mysql-apt-config_0.8.3-1_all.deb
```
Теперь можно установить полученный файл (понадобятся права администратора):
```bash
sudo dpkg -i mysql-apt-config*
```
Во время установки вам будет представлен экран конфигурации, с помощью которого вы можете указать, какую версию MySQL нужно использовать, и установить репозитории других инструментов, связанных с MySQL:

![Экран установки репозитория MySQL](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-apt-mysql-01.png?raw=true "Экран установки репозитория MySQL")

По умолчанию файл добавит информацию только о репозитории последней стабильной версии MySQL для выбронной ветки. Выберем ветку 5.7:

![Выбираем ветку 5.7 репозитория MySQL](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-apt-mysql-02.png?raw=true "Выбираем ветку 5.7 репозитория MySQL")

После выберем **Ok** и **Enter**.

![Заканчиваем подключение репозитория MySQL](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-apt-mysql-03.png?raw=true "Заканчиваем подключение репозитория MySQL")

После этого репозиторий будет добавлен на сервер. Обновим индекс пакетов (нужны права администратора):
```bash
sudo apt-get update
```
Репозиторий MySQL добавлен. Если нужно будет обновить конфигурацию этих репозиториев, достаточно запустить `sudo dpkg-reconfigure mysql-apt-config` и повторно обновить индекс пакетов `sudo apt-get update`

Теперь последнюю версию MySQL.

## установка MySQL

Установка сервера базы данных MySQL исключительно проста. Достаточно одной команды (понадобятся права администратора):
```
sudo apt-get install mysql-server
```
В процессе установки будет дважды запрошен пароль ***root*** -- сперпользователя базы. На «боевых» серверах рекомендуется, чтобы пароль был посложнее и отличался от пароля нашего, текущего (и всех остальных) пользователей. Но сейчас это не важно. Главное, что мы будем использовать этот пароль дальше в инструкции, то обозначим его: «_secret_password_mysql_root_».








2: Установка MySQL
Установите новую версию MySQL:

sudo apt-get install mysql-server

Менеджер apt просмотрит все доступные пакеты mysql-server и выберет наиболее новую версию MySQL. Затем он определит зависимости программы и предложит подтвердить установку. Для этого нажмите y и Enter. После этого будет предложено установить root-пароль. Выберите и подтвердите надёжный пароль.

СУБД MySQL установлена и запущена. Проверьте состояние MySQL:

systemctl status mysql
mysql.service - MySQL Community Server
Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
Active: active (running) since Wed 2017-04-05 19:28:37 UTC; 3min 42s ago
Main PID: 8760 (mysqld)
CGroup: /system.slice/mysql.service
└─8760 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

Если в выводе есть строка Active: active (running), значит, СУБД успешно установлена и запущена.

3: Безопасность MySQL
MySQL поставляет команду, с помощью которой можно повысить безопасность свежей установки. Запустите её:

mysql_secure_installation

Команда запросит root-пароль MySQL. Введите его и нажмите Enter. После этого команда задаст вам ряд вопросов.

Для начала она предложит включить плагин проверки валидности паролей (он автоматически применяет определенные правила защиты паролей пользователей MySQL). Необходимость этого плагина полностью зависит от индивидуальных потребностей сервера. Чтобы включить его, введите y и Enter; чтобы пропустить этот вопрос, просто нажмите Enter. После включения плагина вам будет предложено выбрать уровень строгости проверки пароля (от 0 до 2). Выберите уровень и нажмите Enter, чтобы продолжить. Затем команда предложит изменить root-пароль. Поскольку это свежая установка и пароль был выбран совсем недавно, вы можете не менять его. Чтобы продолжить, нажмите Enter.

На остальные вопросы можно ответить yes. Команда предложит удалить анонимных пользователей MySQL, запретить удаленный root-доступ, удалить тестовую базу данных и перезагрузить привилегии, чтобы все изменения вступили в силу. Введите y и нажмите Enter в каждом новом окне.

Сценарий завершит свою работу после того как вы ответите на все вопросы.

4: Тестирование установки MySQL
mysqladmin – это клиент командной строки MySQL. Используйте его, чтобы подключиться к серверу и вывести  некоторую информацию о версии и состоянии MySQL:

mysqladmin -u root -p version

С помощью -u root клиент mysqladmin подключается как root- пользователь MySQL; флаг –p включает поддержку пароля, а version – это команда, которую нужно запустить.

В выводе вы увидите версию сервера MySQL, время его безотказной работы и некоторую другую информацию о состоянии.

mysqladmin  Ver 8.42 Distrib 5.7.17, for Linux on x86_64
Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Server version      5.7.17
Protocol version    10
Connection      Localhost via UNIX socket
UNIX socket     /var/run/mysqld/mysqld.sock
Uptime:         58 min 28 sec
Threads: 1  Questions: 10  Slow queries: 0  Opens: 113  Flush tables: 1  Open tables: 106  Queries per second avg: 0.002

Если вы получили такой результат, установка свежей версии MySQL прошла успешно!











После установки MySQL автоматически запустится. Заходим в него под root-пользователем.
```bash
mysql -u root -p secret_password_mysql_root
```

Появится сообщение:
```sql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 136
Server version: 5.5.58-0+deb8u1 (Debian)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Поздравляю, мы внутри консольного клиента MySQL. Создаём базу данных нашего Joomla-проекта (в нашем случае потребуется две базы **intranet** и **phones2**:
```sql
CREATE DATABASE intranet DEFAULT CHARACTER SET cp1251 DEFAULT COLLATE cp1251_general_ci;
CREATE DATABASE phones2 DEFAULT CHARACTER SET latin1 DEFAULT COLLATE latin1_swedish_ci;
```
Затем, чтобы наше приложение не работало с базой под аккаунтом супер-пользователя **root**, создаём нового пользователя базы **[db-user]** с паролем «_secret_password_mysql_user_»:
```sql
GRANT ALL PRIVILEGES ON intranet.* TO '[db-user]'@'localhost' IDENTIFIED BY '_secret_password_mysql_user_';
GRANT ALL PRIVILEGES ON phones2.* TO '[db-user]'@'localhost' IDENTIFIED BY '_secret_password_mysql_user_';
```

Если по какой-либо причине, нам понадобится удалённый доступ от имени этого пользователя (например, для работы с базой посредством удалённого клиент-менеджера, на подобии dbForge Studio или MySQL Workbench), то можно заменить `'localhost'` на `'%'`. Если захотим разрешить этому пользователю доступ и ко всем существующим базам в нашем MySQL-сервере, то необходимо заменить `PRIVILEGES ON intranet.*` на  `PRIVILEGES ON *.*`.

Перед выходом из MySQL стоит проверить, что в ней установлен правильный часовой пояс:
```sql
SHOW VARIABLES LIKE '%time_zone%';
```

Работа с базой закончена. Можно выйти из MySQL
```sql
quit;
```

Если мы создали MySQL-пользователя с возможностью удалённого доступа, то нам понадобиться изменить конфигурационный файл MySQL. Открываем его на редактирование (понадобятся права администратора):
```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
Находим строчку `bind-address = 127.0.0.1` и комментируем её `# bind-address = 127.0.0.1` или, если даём доступ только с определённого IP, то указываем его: `bind-address = 192.168.1.40`. Более сложные правила доступа следует настраивать через *firewall*.

Туда же, в блок `[mysqld]` можно добавить инструкции для последующего использования юнкоровской кодировки **utf-8** (или любой другой) по умолчанию при создании таблиц и баз данных.
```python
character-set-server    = utf8
collation-server        = utf8_general_ci
```

Сохраняем конфиг-файл MySQL `Ctrl+O`, `Enter` и `Ctrl+X`, а затем, перезапускаем MySQL чтобы изменения конфигурационного файла подействовали (понадобятся права администратора):
```
sudo service mysql restart
```

MySQL и база проекта настроены. Теперь нам нужно [установить и настроить веб-сервер nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).



--------------------------

## Исключительные случаи

Если при установке запрос ***root***-суперпользователя базы не будет запрошен (иногда случаете), то его придётся установить вручную. Для этого можно воспользоваться [инструкциями по смене root-пароля из интернет](https://www.8host.com/blog/sbros-parolya-root-v-mysql-i-mariadb/)

Иногда, при установке MySQL может установиться MirandaDB (сейчас идёт массовая замена дистрибивов и отказ от Oracle). Miranda DB - бинарно-совместима с MySQL.
