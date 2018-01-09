### ������������ ������������ ���-������� ��� ������ ������� �� PHP 5.2.17

### ���������� �����:

* 1. [��������� ������� Debian (��� �������) � ��������� ���������](first-install-and-adjust-debian.md).

# 2: ��������� SSH � ��������� �������

## ������������ ������ root � ��������� ������������ SSH

����� ���������� ������������ ������������ � ����� ����������� ����� �� ����� �������� �� ��� ������ �� SSH ��� �����, ������ ��� ���������, ������������� ***[user]*** � ������� ***[user_pwd]***. ������ ������� ������� ��������� ��������� � ����� `/etc/ssh/sshd_config` -- ������������ ssh-�������:

```sudo
sudo nano /etc/ssh/sshd_config
```

������ �������� �������� `sshd_config` ��������� [�� ����� Ubuntu](http://help.ubuntu.ru/wiki/ssh). ��� ���������� ������ �������� ������������ �� ���������� ������������. � ����� ������ ������ ���������� � ����� ��������� ��� ������: � ��������� ssh-���� ������������ **root** (�������������� �� ���������: **ubuntu** ��� ������ Ubuntu, **pi** ��� ������ ��� Raspberry Pi?  � ��� �����) � ��������� ������ ������, ����� ���������� ������������ **[user]**:

```cfg
DenyUsers root
AllowUsers [user]
```

����� ��������� ������������� ����� ������������� ssh-������:

```bash
sudo service ssh restart
```

������������� -- `logout`. ������ ��� ��� � ����� ���������, ��� ������ ������������ **root** ������ � ������� �� SSH �� �������. ���������� ����� ��������� ������������� **[user]**.

## ��������� ������������� ������� ��� �����

� ���������������� ����� `/etc/ssh/sshd_config` ���� ����������� �������� `Banner` � ������� ����������� ����, ������� ������� �������� ��� ����� (������ `/etc/ssh_banner`). ��� ����������� �������� ������������� ������� ��� �����, ��� ������� ������� � �������, �������� ����� ������ ��� ������������ � ����������� ���������� ��������. �� �� ���� ������ ������������ ������ ��� ����� ����� SSH. ���������� ����� ����������� �������, �������������� � ��� ������� ����. ��� �������� ������������� ���� ���� �� �������� � ������� (��������, � ������ ���������� ����������� �������� �� ����� ����������).

������� ���� `/etc/motd`, � �������� ���� [�����-������ �������� ASCII-��� ����� �� ASCII-����������](http://patorjk.com/software/taag/#p=display&f=Ogre&t=intranet-Z). :

```bash
sudo nano /etc/motd
```

��������, ��� ���:

```
 _       _�����-����, ������-����_2017.12_____
(_)_ __ | |_ _ __ __ _ _ __   ___| |_    / _  /
| | '_ \| __| '__/ _` | '_ \ / _ \ __|___\// /
| | | | | |_| | | (_| | | | |  __/ ||_____/ //\
|_|_| |_|\__|_|  \__,_|_| |_|\___|\__|   /____/
```

��������� ������ `Ctrl+O` � `Enter`, � ������� �� ��������� `Ctrl+X`.

## ���������� ��������� ���������� ��� �����

����� ������� ��� ����� � ������� (�� ������ �� SSH) ���������� ������������ ��������� ����������. ��� ����� ���� � ����� `/etc/profile.d/` �������� ������, ������� ����� ���������� ��� ������ �����. ��������� ���� �� �������������� (����� ����� ��������������):

```bash
sudo nano /etc/profile.d/sshinfo.sh
```

�������� � ����, ��������, ��������� ������:

```bash
SystemMountPoint="/";
LinesPrefix="  ";
b=$(tput bold); n=$(tput sgr0);

SystemLoad=$(cat /proc/loadavg | cut -d" " -f1);
ProcessesCount=$(cat /proc/loadavg | cut -d"/" -f2 | cut -d" " -f1);

MountPointInfo=$(/bin/df -Th $SystemMountPoint 2>/dev/null | tail -n 1);
MountPointFreeSpace=( \
  $(echo $MountPointInfo | awk '{ print $6 }') \
  $(echo $MountPointInfo | awk '{ print $3 }') \
);
UsersOnlineCount=$(users | wc -w);

UsedRAMsize=$(free | awk 'FNR == 3 {printf("%.0f", $3/($3+$4)*100);}');

SystemUptime=$(uptime | sed 's/.*up \([^,]*\), .*/\1/');

if [ ! -z "${LinesPrefix}" ] && [ ! -z "${SystemLoad}" ]; then
  echo -e "${LinesPrefix}${b}��������:${n}\t${SystemLoad}\t\t\t${LinesPrefix}${b}���������:${n}\t\t${ProcessesCount}";
fi;

if [ ! -z "${MountPointFreeSpace[0]}" ] && [ ! -z "${MountPointFreeSpace[1]}" ]; then
  echo -ne "${LinesPrefix}${b}���� $SystemMountPoint:${n}\t${MountPointFreeSpace[0]} �� ${MountPointFreeSpace[1]}\t\t";
fi;
echo -e "${LinesPrefix}${b}������������� ������:${n}\t${UsersOnlineCount}";

if [ ! -z "${UsedRAMsize}" ]; then
  echo -ne "${LinesPrefix}${b}������:${n}\t${UsedRAMsize}%\t\t\t";
fi;
echo -e "${LinesPrefix}${b}���������� ��������:${n}\t${SystemUptime}";
```

��������� `Ctrl+O` � `Enter`, � ������� �� ��������� `Ctrl+X`. ����� ���� ���� ����� �� ������ ����� ������� (����� ����� ��������������):

```bash
sudo chmod +x /etc/profile.d/sshinfo.sh
```

## ������������ �������� bash

��� ����� � ������� ��������������� ����������� ������� �� ����� `/etc/update-motd.d`. ��� ������������ ����� ����������� � ��������� ����������. ������ ����� ����������� ��������� ����� ������ �������� ���������� �������� ��������:

```bash
sudo chmod -x /etc/update-motd.d/10-help-text
sudo chmod -x /etc/update-motd.d/51-cloudguest
```

������ ������� ������ `/etc/update-motd.d/50-landscape-sysinfo`, ������� ��������� ����� ������ �������������� ���������� � ��������� �������. ������ ��� ��������� �������������� ������, ��������� �� ������� ���������� � �������:

```bash
sudo apt install landscape-common
```

� ����� ������� ��������-������������ ���� ��������� ������ ������� �� �������������� ���� �������� �������� bash ������������ `.bashrc':

```bash
nano ~/.bashrc
```

������� ��� ������ `#force_color_prompt=yes` ��������������� � (� ������� � ��� `#`). � ����� ������ �����, ������� ����:

```bash
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
```

� ������ �� ����

```bash
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u\[\033[01;33m\]@\[\033[01;32m\]\h\[\033[00m\]:\[\033[00;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
```

��! ���������������� ����� ��������� �������������.

```bash
logout
```


---------------

## ��������� �����:

* 3. [��������� � ��������� MySQL](install-and-adjust-MySQL-f�r-php-5.2.17.md).
* 4. [������ � ��������� ����������� PHP 5.2.17 ������ � FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [��������� � ��������� ���-������� nginx](install-and-adjust-nginx-f�r-php-5.2.17.md).
* 6. [��������� Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) � ��������� phpMyAdmin.
* 7. [��������������� ��������� LDAP](adding-ldap-to-debian-nginx-and-php.md) -- ������ � Active Directory.
* 8. [������ ������� �� ������ �������](claen.md).
