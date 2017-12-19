# Установка и настройка сервера MySQL

Установка сервера базы данных MySQL исключительно проста. Достаточно одной команды (понадобятся права администратора):
```
sudo apt-get install mysql-server
```
В процессе установки будет дважды запрошен пароль ***root*** -- сперпользователя базы. На «боевых» серверах рекомендуется, чтобы пароль был посложнее и отличался от пароля нашего, текущего (и всех остальных) пользователей. Но сейчас это не важно. Главное, что мы будем использовать этот пароль дальше в инструкции, то обозначим его: «_secret_password_mysql_root_».

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
