### ������������ ������������ ���-������� ��� ������ ������� �� PHP 5.2.17

### ���������� �����:

* 1. [��������� ������� Debian (��� �������) � ��������� ���������](first-install-and-adjust-debian.md).
* 2. [��������� SSH � ��������� �������](ssh-tuning.md).
* 3. [��������� � ��������� MySQL](install-and-adjust-MySQL-f�r-php-5.2.17.md).
* 4. [������ � ��������� ����������� PHP 5.2.17 ������ � FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [��������� � ��������� ���-������� nginx](install-and-adjust-nginx-f�r-php-5.2.17.md).
* 6. [��������� Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) � ��������� phpMyAdmin.
* 7. [��������������� ��������� LDAP](adding-ldap-to-debian-nginx-and-php.md) � ������ � Active Directory.

# 8: ������ ������� �� ������

� �������� ������ PHP ��� �������� ���������� ���� ������� ������������, ��������� �������, ������� ������ � ������ ��������� �����. ����� �� ���������� ������� ����� ��������� ������� � ���������� ���� �� �������� ������.

������� ����� � ��������� ������ ������� � ������� �� �������� �����:
```bash
rm $HOME/nginx_signing.key
rm $HOME/php-5.2.17-libxml2.patch
rm $HOME/php-5.2.17-openssl.patch
rm $HOME/php-5.2.17.tar.gz
rm $HOME/php-fpm.diff.gz
rm -r $HOME/php-5.2.17
```

# �Ѩ
# �����

-----------------------

### *��� ����� ��������:*

����� ����������� ���������������� �������� ������ ������������. � ��� ���� ������������ ��������� ������: `libxml2-dev`, `libmysqlclient-dev`, `libcurl4-gnutls-dev`, `libpng12-dev`, `libjpeg62-turbo-dev`, `libjpeg-dev`, `libxslt1-dev`, `libbz2-dev`, `libmcrypt-dev`, `libmhash-dev`, `libfcgi-dev`, `libmhash-dev`, `libssl-dev`, `libcrypto++-dev`, `lib32z1-dev`, `libltdl-dev`, `libcurl4-openssl-dev`, `libexpat1-dev`, `libsasl2-dev`, `python-dev`, `libldap2-dev`, `libssl-dev` � `libpcre3-dev`

��� ������������� ��������� ����� ��������������:

```bash
sudo apt-get purge ���-������
```

����� ��������� ������ ������������� ������ ��������� ����� �������. ��������� ����� �� ������ ������� ��������� ������� � �������. ������ ������������� ������� �� ������. ����� ������� �������� ��������� ����������� � ��������� ����������������� ���� �������. ����� �������� ������� ������ ������ �������������� (����������) � ��������� ������, ������� �� ��������� ���������������� � ������� ���������� ������� (��������� ����� ��������������):

```bash
sudo apt-get autoremove
```

