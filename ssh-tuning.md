# Настраиваем SSH-доступ

По идее можно разлогироваться и заходить по SSH под новым, только что созданным пользователем, но можно сразу сделать небольшие изменения в файле `/etc/ssh/sshd_config` -- конфигурации ssh-доступа:

```sudo
sudo nano /etc/ssh/sshd_config
```

Полное описание настроек `sshd_config` находится [на сайте Ubuntu](http://help.ubuntu.ru/wiki/ssh). Там содержатся вполне разумные рекомендации по увеличению безопасности. Но в нашем случае к ним нужно немного добавить. Дописываем в конце следующие две строки, и запрещаем ssh-вход пользователя **ubuntu** (пользователю по умолчанию) и разрешаем доступ нашему вновь созданному пользователю **[user]**:
```cfg
DenyUsers root
AllowUsers [user]
```

Также стоит удалить всякую неинформативную фигню из login-приветсвия, очистив файл `/etc/motd`, и поместив туда [какой-нибудь красивый ASCII-арт текст из ASCII-генератора](http://patorjk.com/software/taag/#p=display&f=Ogre&t=intranet-Z):
```bash
sudo nano /etc/motd
```
Например, вот так:
```
 _       _«Чуки-чуки, банана-куки»_2017.12_____
(_)_ __ | |_ _ __ __ _ _ __   ___| |_    / _  /
| | '_ \| __| '__/ _` | '_ \ / _ \ __|___\// /
| | | | | |_| | | (_| | | | |  __/ ||_____/ //\
|_|_| |_|\__|_|  \__,_|_| |_|\___|\__|   /____/
```
Сохраняем баннер `Ctrl+O` и `Enter`, а выходим из редактора `Ctrl+X`.

Очень полезно при входе в систему (не только по SSH) отобразать служебную информацию о загрузке системы. Для этого надо в ппапку `/etc/profile.d/` добавить скрипт, который будет исполнятся при каждом входе. Отнрываем вайл на редактирование (нужны права администратора):
```bash
sudo nano /etc/profile.d/sshinfo.sh
```
Помещаем в него следующий скрипт:
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
Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X`. Затем надо дать право на запуск этого скрпита (нужны права администратора):
```bash
sudo chmod +x /etc/profile.d/sshinfo.sh
```

Чтобы настройки подействовали нужно перезапустить ssh-сервис:
```bash
sudo service ssh restart
```

Разлогируемся. Можем тут же проверить, что теперь пользователя **pi** больше в систему по SSH не пускают. Логируется вновь созданным пользователем **[user]** и мы готовы перейти к настройкам проекта. Но...

## «Вишенка на торте» -- настраиваем сообщение при входе в систему и раскрашиваем оболочку bash

При входе в систему последовательно выполняются скрипты из папки `/etc/update-motd.d`. Они обеспечивают вывод приглашения и служебной информации. Убрать часть бесполезных подсказок можно просто запретив исполнение ненужных скриптов:
```bash
sudo chmod -x /etc/update-motd.d/10-help-text
sudo chmod -x /etc/update-motd.d/51-cloudguest
```
Теперь добавим скрипт `/etc/update-motd.d/50-landscape-sysinfo`, который обеспечит вывод важной дополнительной информации о состоянии системы. Заодно это установит дополнительные пакеты, некоторые из которых пригодятся в проекте:
```bash
sudo apt install landscape-common
```

И чтобы сделать красочно-разноцветным нашу командную строку откроем на редактирование файл настроек оболочки bash пользователя `.bashrc':
```bash
nano ~/.bashrc
```
находим там строку `#force_color_prompt=yes` раскомментируем её (и удаляем в ней `#`). И чтобы совсем отпад, находим блок:
```bash
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
```
и меняем на блок
```bash
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u\[\033[01;33m\]@\[\033[01;32m\]\h\[\033[00m\]:\[\033[00;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
```

Все! Перелогиниваемся чтобы настройки подействовали. Дальше нам понадобится сервер базы данных МySQL. Установим его:

-----