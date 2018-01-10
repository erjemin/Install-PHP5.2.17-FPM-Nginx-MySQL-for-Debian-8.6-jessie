### ������������ ������������ ���-������� ��� ������ ������� �� PHP 5.2.17

### ���������� �����:

* 1. [��������� ������� Debian (��� �������) � ��������� ���������](first-install-and-adjust-debian.md).
* 2. [��������� SSH � ��������� �������](ssh-tuning.md).
* 3. [��������� � ��������� MySQL](install-and-adjust-MySQL-f�r-php-5.2.17.md).
* 4. [������ � ��������� ����������� PHP 5.2.17 ������ � FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [��������� � ��������� ���-������� nginx](install-and-adjust-nginx-f�r-php-5.2.17.md).
* 6. [��������� Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) � ��������� phpMyAdmin.

# 7: ��������� Joomla! 1.0.15

��� ������� ������ ����� ����� ������ ����������� ��� ����� ������� � ����� `home/[user]/[site]/html`.
����� ���� ��������������� ���� 'configuration.php' � ���:

```bash
nano /home/[user]/[site]/html/configuration.php
```
����� ���� ����� � �������� � ��� ��������� �����:

```php
$mosConfig_live_site = 'http://[ip-������-�������]/';
$mosConfig_absolute_path = '/home/[user]/[site]/html';
$mosConfig_cachepath = '/home/[user]/[site]/html/cache';
$mosConfig_user = '[db-user]';
$mosConfig_password = '[_secret_password_mysql_user_]';
$mosConfig_db = 'intranet';
```

�� �������� ������ **[ip-������-�������]**, **[user]**, **[site]**, **[db-user]** � **[_secret_password_mysql_user_]** �� ��������� ��������.

�� ������. ��� ������ ���� ������ �������� � ����������� ��� ������ *http://[ip-������-�������]*

--------------

## ��������� �����:

* 7. [��������������� ��������� LDAP](adding-ldap-to-debian-nginx-and-php.md) - ������ � Active Directory.
* 8. [������ ������� �� ������ �������](claen.md).