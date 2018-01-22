### ������������ ������������ ���-������� ��� ������ ������� �� PHP 5.2.17

### ���������� �����:

* 1. [��������� ������� Debian (��� �������) � ��������� ���������](first-install-and-adjust-debian.md).
* 2. [��������� SSH � ��������� �������](ssh-tuning.md).
* 3. [��������� � ��������� MySQL](install-and-adjust-MySQL-f�r-php-5.2.17.md).
* 4. [������ � ��������� ����������� PHP 5.2.17 ������ � FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [��������� � ��������� ���-������� nginx](install-and-adjust-nginx-f�r-php-5.2.17.md).
* 6. [��������� Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) � ��������� phpMyAdmin.
* 7. [��������������� ��������� LDAP](adding-ldap-to-debian-nginx-and-php.md) � ������ � Active Directory.
* 8. [������ ������� �� ������ �������](claen.md).

# 9: ��������� ����, ��������� ����������� � �������������� ����� � ������������.

## ������� ��������� ������

������������� ���������� ������� ����� �� ���������� �� ������������� ���������. ������������� ����������� ���� ����� ������������ ������� -- **[user]**, ������ ������������ -- **[user_pwd]**, ��� ������������ ���� ������ MySQL -- **[db-user]** � ��� ������ **[secret_password_mysql_user]** � ����� ��� ������������ ����� -- **[site]**.

��� ���� ���� ���������, ��� � ���������� ����� ����� ������ **[ip-������-�������]** (������ ��� **[ip-���������-�������]**) � ����� ��� �����. � ��� ���� ������ � ���������������� ����� nginx `$HOME/[site]/config/[site]_nginx.conf` (��. [�������������� ������ � ����� ���������� � ��������� ���-������� nginx�](install-and-adjust-nginx-f%D0%BEr-php-5.2.17.md#%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-nginx-%D0%B4%D0%BB%D1%8F-%D0%BD%D0%B0%D1%88%D0%B5%D0%B3%D0%BE-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B0)) � Joomla `$HOME/[site]/html/configuration.php` (��. ��. [�������������� ������ � ����� ���������� Joomla! 1.0.15�](settings-config-for-Joolma-1.0.15.md#7-%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-joomla-1015)).

���� ��� ������ �������� ��� ����������� ������, ����� ����� ������ � ������������. �� ��� ���� ���� ������, ��� � ������ �������� ������� ����� �������� ��� ���������� � �����. ��� ��������� ��������� � ����� `/etc/hostname`, � ����� � `/etc/hosts`. ��� �� �������������� ��������� ����� ��������������. ��������� �������� ����������� ����� �����������.

## Backup-������

### ���������������� ����

��� �������� ������� ��� ����������� zip. ��������� ��� (����� ����� ��������������):

```bash
sudo apt-get install zip
```

### ������ ������� backup

��� ����������� �������� ���� ���� ��� �� MySQL, ������� ������ � ������ ������� � ��������������� ����� �����. �������� ������   � ����� **[site]**:

```bash
nano ~/[site]/backup.sh
```

� �������� � ���� ��������� ������� (�� �������� ������ **[db-user]** � **[secret_password_mysql_user]** �� ���� ��������):

```bash
echo "���� � ������������� ����!";

if ls -d db-backup-intranet-*.sql.zip >/dev/null 2>&1;
  then rm db-backup-intranet-*.sql.zip -f;
fi;
echo -ne ">> ���� INTRANET >>";
mysqldump -u[db-user] -p[secret_password_mysql_user] intranet > intranet.sql
zip db-backup-intranet-$(date +%Y-%m-%d~%H:%M:%S).sql.zip intranet.sql -9;
rm intranet.sql -f;
echo -ne ">> ������        >>  ";
ls -sh --color=always db-backup-intranet-*.sql.zip;

if ls -d db-backup-phones2--*.sql.zip >/dev/null 2>&1;
  then rm db-backup-phones2--*.sql.zip -f;
fi;
echo -ne ">> ���� PHONES2  >>";
mysqldump -u[db-user] -p[secret_password_mysql_user] phones2 > phones2.sql
zip db-backup-phones2--$(date +%Y-%m-%d~%H:%M:%S).sql.zip phones2.sql -9;
rm phones2.sql -r;
echo -ne ">> ������        >>  ";
ls -sh --color=always db-backup-phones2--*.sql.zip;
echo -ne ">> ���� REESTR   >>";

if ls -d db-backup-reestr---*.sql.zip >/dev/null 2>&1;
  then rm db-backup-reestr---*.sql.zip -f;
fi;
mysqldump -u[db-user] -p[secret_password_mysql_user] reestr > reestr.sql
zip db-backup-reestr---$(date +%Y-%m-%d~%H:%M:%S).sql.zip reestr.sql -9; 
rm reestr.sql -f;
echo -ne ">> ������        >>  ";
ls -sh --color=always db-backup-reestr--*.sql.zip;

echo ""
echo "������������� �������� HTML";
if ls -d html-backup--------*.zip >/dev/null 2>&1;
  then rm html-backup--------*.zip -f;
fi;
zip html-backup--------$(date +%Y-%m-%d~%H:%M:%S).zip html -r9 -qq; 
echo -ne ">> ������        >>  ";
ls -sh --color=always html-backup-*.zip;
echo ""
df -h
echo "";
echo " _._     _,-'\"\"\`-._";
echo "(,-.\`._,'(       |\\\`-/|";
echo "    \`-.-' \\ )-\`( , o o)";
echo " ���!     \`-    \\\`_\`\"'-  �� ����������! ��� ��� ����� �� ��������!";
echo "";
```
��������� `Ctrl+O` � `Enter`, � ������� �� ��������� `Ctrl+X`.

��������� ��� ������. ��������� � ����� � ������ � �������� ���:

```bash
cd [site]
bash backup.sh
```

� ����� ������� (����� ���������... ������ � �������� �������� ���� �� �������):

```txt
���� � ������������� ����!
>> ���� INTRANET >>  adding: intranet.sql (deflated 80%)
>> ������        >>  22M db-backup-intranet-2018-01-22~16:43:51.sql.zip
>> ���� PHONES2  >>  adding: phones2.sql (deflated 84%)
>> ������        >>  64K db-backup-phones2--2018-01-22~16:44:06.sql.zip
>> ���� REESTR   >>  adding: reestr.sql (deflated 66%)
>> ������        >>  20K db-backup-reestr---2018-01-22~16:44:06.sql.zip

������������� �������� HTML
>> ������        >>  5,0G html-backup--------2018-01-22~16:44:06.zip

�������� ������� ������ ������������  ���� ������������% C����������� �
/dev/sda1           15G          13G  1,5G           91% /
udev                10M            0   10M            0% /dev
tmpfs               99M         4,4M   95M            5% /run
tmpfs              248M            0  248M            0% /dev/shm
tmpfs              5,0M            0  5,0M            0% /run/lock
tmpfs              248M            0  248M            0% /sys/fs/cgroup

 _._     _,-'""`-._
(,-.`._,'(       |\`-/|
    `-.-' \ )-`( , o o)
 ���!     `-    \`_`"'-  �� ����������! ��� ��� ����� �� ��������!
```

��������, ��� ������ ��������� � � ����� ��������� ������:

```bash
ls -sh1t
```

������� �������� ����� �����:

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

������ �������� � ��������� ����� ���������.

������ ����� ��������� ��������������� ���� �� ���� ��������� �����.


## Restore-������

### ���������������� ����

��� ��������� ������� ��� ����������� unzip. ��������� ��� (����� ����� ��������������):

```bash
sudo apt-get install unzip
```

### ������ ������� restore

��� ����������� ����� ����� ������ ����� ������ ������ ���� (���� ���� ������� ���������), ����������� ������ �� ���, ����������� ���� � MySQL, ������� ������� �����������, �������������, ���� �����. � �-���� ��������� ����� �����, � ������� �����. ��� ���� ��� ���������� ����������� ������ ���������� �����, �� ��� ���� �������� �� ��������� ���������������� ���� ����� -- `$HOME/[site]/html/configuration.php`.  �������� ������ � ����� **[site]**:

```bash
nano ~/[site]/restore.sh
```

� �������� � ���� ��������� ������� (�� �������� ������ **[db-user]** � **[secret_password_mysql_user]** �� ���� ��������):

```bash
echo "�������������� ���� �� ����� � �����������!";
echo "?";
echo -ne "??������������� ���� INTRANET: ";
if ls -d db-backup-intranet-*.sql.zip >/dev/null 2>&1;
  # �������� ����� ������ ���� �� ���������
  FileName=$(ls -tN db-backup-intranet-*.sql.zip | head -1);
  echo $FileName;
  then
    if [ -f "intranet.sql" ]; then rm intranet.sql; fi;
    unzip -q $FileName;
    if [ $? -ne 0 ];then echo "������: �� ������� ����������� �����!"; exit 1; fi
  else  echo "������: ��� ����� ������!";  exit 1;
fi;
echo -ne "? �������:			 ";
ls -sh --color=always intranet.sql;
echo -ne "? �������� ���� � MySQL:	 ";
mysql -u[db-user] -p[secret_password_mysql_user] intranet < intranet.sql
if [ $? -ne 0 ];
  then echo "������: ���� �� ��������! ��� ������� MySQL, ��� ����, ��� ��������� ���-�� ����������!"; exit 1;
else echo "Ok!" ;fi;
rm intranet.sql;

echo "?";
echo -ne "??������������� ���� PHONES2:	 ";
if ls -d db-backup-phones2--*.sql.zip >/dev/null 2>&1;
  # �������� ����� ������ ���� �� ���������
  FileName=$(ls -tN db-backup-phones2--*.sql.zip | head -1);
  echo $FileName;
  then
    if [ -f "phones2.sql" ]; then rm phones2.sql; fi;
    unzip -q $FileName;
    if [ $? -ne 0 ];then echo "������: �� ������� ����������� �����!"; exit 1; fi
  else  echo "������: ��� ����� ������!";  exit 1;
fi;
echo -ne "? �������:			 ";
ls -sh --color=always phones2.sql;
echo -ne "? �������� ���� � MySQL:	 ";
mysql -u[db-user] -p[secret_password_mysql_user] phones2 < phones2.sql
if [ $? -ne 0 ];
  then echo "������: ���� �� ��������! ��� ������� MySQL, ��� ����, ��� ��������� ���-�� ����������!"; exit 1;
else echo "Ok!" ;fi
rm phones2.sql;

echo "?";
echo -ne "??������������� ���� REESRT:	 ";
if ls -d db-backup-reestr---*.sql.zip >/dev/null 2>&1;
  # �������� ����� ������ ���� �� ���������
  FileName=$(ls -tN db-backup-reestr---*.sql.zip | head -1);
  echo $FileName;
  then
    if [ -f "reestr.sql" ]; then rm reeset.sql; fi;
    unzip -q $FileName;
    if [ $? -ne 0 ];then echo "������: �� ������� ����������� �����!"; exit 1; fi
  else  echo "������: ��� ����� ������!";  exit 1;
fi;
echo -ne "? �������:			 ";
ls -sh --color=always reestr.sql;
echo -ne "? �������� ���� � MySQL:	 ";
mysql -u[db-user] -p[secret_password_mysql_user] reestr < reestr.sql
if [ $? -ne 0 ];
   then echo "������: ���� �� ��������! ��� ������� MySQL, ��� ����, ��� ��������� ���-�� ����������!"; exit 1;
else echo "Ok!" ;fi
rm reestr.sql;

echo "?";
echo -ne "??���������� ������ HTML:	 ";
if ls -d html-backup--------*.zip >/dev/null 2>&1;
  # �������� ����� ������ ���� �� ���������
  FileName=$(ls -tN html-backup--------*.zip | head -1);
  then
    unzip -t -qq $FileName;
    if [ $? -ne 0 ];
      then echo "������: �������� ����������� ������!"; exit 1;
      else
        echo "Ok!";
        echo "?";
        echo -ne "??������������� ����� HTML:	 ";
        unzip -u -o -C -qq $FileName -x html/configuration.php;
        if [ $? -ne 0 ];
          then echo "������: �� ������� �����������!";
        else echo "Ok!";
        fi;
    fi;
  else  echo "������: ��� ����� ������!";  exit 1;
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
��������� `Ctrl+O` � `Enter`, � ������� �� ��������� `Ctrl+X`.

��������� ��� restore-������. ��������� � ����� � ������ � �������� ���:

```bash
cd [site]
bash restore.sh
```

� ����� ������� (����� ���������... ������ � �������� �������� ���� �� �������):

```txt
�������������� ���� �� ����� � �����������!
?
??������������� ���� INTRANET: db-backup-intranet-2018-01-22~16:43:51.sql.zip
? �������:			 105M intranet.sql
? �������� ���� � MySQL:	 Ok!
?
??������������� ���� PHONES2:	 db-backup-phones2--2018-01-22~16:44:06.sql.zip
? �������:			 376K phones2.sql
? �������� ���� � MySQL:	 Ok!
?
??������������� ���� REESRT:	 db-backup-reestr---2018-01-22~16:44:06.sql.zip
? �������:			 55K reestr.sql
? �������� ���� � MySQL:	 Ok!
?
??���������� ������ HTML:	 Ok!
?
??������������� ����� HTML:	 Ok!
?
�������� ������� ������ ������������  ���� ������������% C����������� �
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

��� �������� ����� ������� ��������� ����� ��� ����� �� �������� `~/[site]/html` � ���������, ��� ����� ��������� ������ ������� `restore.sh` ��� ����� � �������� �����������������.

������ ����� ������� �������� ������ ���������� backup-����� �� ��������� ������.


## ������ Send-Backup

### ���������������� ���� -- ����������, ����������� ����� ����� ���������

��� ����. ����� �� ����� ���������� � ������ ������� �� ������ �����, � ��������� ������ �� ��������� ������� �� ����� ������ ��� ������, ����� ��������� ���������� ssh-����� ����� ���������.


