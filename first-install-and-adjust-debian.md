# ��������� ������� Debian (��� �������) � ��������� ���������

![�������� ���������](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-01.png?raw=true "�������� ���������")

2

![���������� ��������� 2](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-02.png?raw=true "���������� ��������� 2")
3

![���������� ��������� 3](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-03.png?raw=true "���������� ��������� 3")

4

![���������� ��������� 4](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-04.png?raw=true "���������� ��������� 4")

5

![���������� ��������� 5](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-05.png?raw=true "���������� ��������� 5")

6

![���������� ��������� 6](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-06.png?raw=true "���������� ��������� 6")

7

![���������� ��������� 7](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-07.png?raw=true "���������� ��������� 7")

8

![���������� ��������� 8](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-08.png?raw=true "���������� ��������� 8")

9

![���������� ��������� 9](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-09.png?raw=true "���������� ��������� 9")

10

![���������� ��������� 10](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-10.png?raw=true "���������� ��������� 10")

11

![���������� ��������� 11](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-11.png?raw=true "���������� ��������� 11")

12

![���������� ��������� 12](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-12.png?raw=true "���������� ��������� 12")

13

![���������� ��������� 13](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-13.png?raw=true "���������� ��������� 13")

14

![���������� ��������� 14](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-14.png?raw=true "���������� ��������� 14")

ip addr

![�������� ��� IP � ������� ������� ip addr](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-get-ip.png?raw=true "ip addr")

## ������������� sudo

��� ��������� ������� � ������� � ��������� ���������������� ������ ���������� ��������� ����� `root`. �������� ������� � ������ �� ��� `root` ����� �������� `su`, � ����� ����� �� ��� 'root' � ������ �������� ������������� **[user]]** �������� `exit`. �� ��������� ������� �� 'root' � **[user]** ��������. ����� ������ ����� �� ��� `root` � ������� ����� ��� ����� ������� ������ ��������� **[user]**. ��� �������� � ������������ ��������� ����� �������� �������� ��� `root` � ����� ��� **[user]** ���������� ����� 'sudo'. �������� � ������ �� ��� `root` � ��������� ���:

```bash
su
apt-get install sudo
```
���� �� ��� �������� ��� `root` ����� �������� �������, ������������ *sudo* ��� �������� ������� � �������������� �������. ��� ������� � ��������� ��������������� ����� `/etc/sudoers`. ��������� ��� �� �������������� � ������� ��������� *nano*:
```bash
nano /etc/sudoers
```
����� ���� ������������ ������ ������ � ������� `sudo` ���������� � ���������������� ���� ����� ������ `root    ALL=(ALL:ALL) ALL` �������� ������ (�� �������� ������ �������� ��� **[user]** �� ���� ������ ������������):
```bash
[user]        ALL=(ALL)       ALL
```
��������� ���������. ��������� ���� `Ctrl+O` � `Enter`, � ����� ������� �� ��������� nano `Ctrl+X`.

�� ��� ��� �������� ��-��� `root`. ������������� ������, ����� ��������� �������� � ����:
```bash
reboot
```

