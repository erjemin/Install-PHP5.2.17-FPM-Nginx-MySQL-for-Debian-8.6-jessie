# ����������� SSH-������

�� ���� ����� ��������������� � �������� �� SSH ��� �����, ������ ��� ��������� �������������, �� ����� ����� ������� ��������� ��������� � ����� `/etc/ssh/sshd_config` -- ������������ ssh-�������:

```sudo
sudo nano /etc/ssh/sshd_config
```

������ �������� �������� `sshd_config` ��������� [�� ����� Ubuntu](http://help.ubuntu.ru/wiki/ssh). ��� ���������� ������ �������� ������������ �� ���������� ������������. �� � ����� ������ � ��� ����� ������� ��������. ���������� � ����� ��������� ��� ������, � ��������� ssh-���� ������������ **ubuntu** (������������ �� ���������) � ��������� ������ ������ ����� ���������� ������������ **[user]**:
```cfg
DenyUsers root
AllowUsers [user]
```

����� ����� ������� ������ ��������������� ����� �� login-����������, ������� ���� `/etc/motd`, � �������� ���� [�����-������ �������� ASCII-��� ����� �� ASCII-����������](http://patorjk.com/software/taag/#p=display&f=Ogre&t=intranet-Z):
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

����� ������� ��� ����� � ������� (�� ������ �� SSH) ���������� ��������� ���������� � �������� �������. ��� ����� ���� � ������ `/etc/profile.d/` �������� ������, ������� ����� ���������� ��� ������ �����. ��������� ���� �� �������������� (����� ����� ��������������):
```bash
sudo nano /etc/profile.d/sshinfo.sh
```
�������� � ���� ��������� ������:
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
  echo -e "${LinesPrefix}${b}System load:${n}\t${SystemLoad}\t\t\t${LinesPrefix}${b}Processes:${n}\t\t${ProcessesCount}";
fi;

if [ ! -z "${MountPointFreeSpace[0]}" ] && [ ! -z "${MountPointFreeSpace[1]}" ]; then
  echo -ne "${LinesPrefix}${b}Usage of $SystemMountPoint:${n}\t${MountPointFreeSpace[0]} of ${MountPointFreeSpace[1]}\t\t";
fi;
echo -e "${LinesPrefix}${b}Users logged in:${n}\t${UsersOnlineCount}";

if [ ! -z "${UsedRAMsize}" ]; then
  echo -ne "${LinesPrefix}${b}Memory usage:${n}\t${UsedRAMsize}%\t\t\t";
fi;
echo -e "${LinesPrefix}${b}System uptime:${n}\t${SystemUptime}";
```
��������� `Ctrl+O` � `Enter`, � ������� �� ��������� `Ctrl+X`. ����� ���� ���� ����� �� ������ ����� ������� (����� ����� ��������������):
```bash
sudo chmod +x /etc/profile.d/sshinfo.sh
```

����� ��������� ������������� ����� ������������� ssh-������:
```bash
sudo service ssh restart
```

�������������. ����� ��� �� ���������, ��� ������ ������������ **pi** ������ � ������� �� SSH �� �������. ���������� ����� ��������� ������������� **[user]** � �� ������ ������� � ���������� �������. ��...

## �������� �� ����� -- ����������� ��������� ��� ����� � ������� � ������������ �������� bash

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

���! ���������������� ����� ��������� �������������. ������ ��� ����������� ������ ���� ������ �ySQL. ��������� ���:

-----