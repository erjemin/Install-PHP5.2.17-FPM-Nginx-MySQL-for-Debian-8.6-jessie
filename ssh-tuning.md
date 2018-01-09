### Развёртывание современного веб-сервера под старые проекты на PHP 5.2.17

### Предыдущие этапы:

* 1. [Установка системы Debian (или аналога) и первичные настройки](first-install-and-adjust-debian.md).

# 2: Настройка SSH и окружения клиента

## Ограничиваем доступ root и повышение безопасности SSH

После завершения перезагрузки произведённой в конце предыдущего этапа мы можем заходить на наш сервер по SSH под новым, только что созданным, пользователем ***[user]*** и паролем ***[user_pwd]***. Теперь следует сделать небольшие изменения в файле `/etc/ssh/sshd_config` -- конфигурации ssh-доступа:

```sudo
sudo nano /etc/ssh/sshd_config
```

Полное описание настроек `sshd_config` находится [на сайте Ubuntu](http://help.ubuntu.ru/wiki/ssh). Там содержатся вполне разумные рекомендации по увеличению безопасности. В нашем случае просто дописываем в конце следующие две строки: и запрещаем ssh-вход пользователя **root** (администратору по умолчанию: **ubuntu** для систем Ubuntu, **pi** для систем для Raspberry Pi?  и так далее) и разрешаем доступ нашему, вновь созданному пользователю **[user]**:

```cfg
DenyUsers root
AllowUsers [user]
```

Чтобы настройки подействовали нужно перезапустить ssh-сервис:

```bash
sudo service ssh restart
```

Разлогируемся -- `logout`. Входим ещё раз и можем проверяем, что теперь пользователя **root** больше в систему по SSH не пускают. Логируется вновь созданным пользователем **[user]**.

## Упрощение идентификации сервера при входе

В конфигурационном файле `/etc/ssh/sshd_config` есть специальный параметр `Banner` в котором указывается файл, который следует показать при входе (обычно `/etc/ssh_banner`). Его отображение упрощает идентификацию сервера при входе, его сложнее спутать с другими, особенно когда работа идёт одновременно с терминалами нескольких серверов. Но он этот баннер отображается только при входе через SSH. Существует более радикальное решение, отображающееся и при обычном воде. Оно упростит идентификацию даже если мы работаем с консоли (например, в случае нескольких виртуальных серверов на одном физическом).

Очистим файл `/etc/motd`, и поместим туда [какой-нибудь красивый ASCII-арт текст из ASCII-генератора](http://patorjk.com/software/taag/#p=display&f=Ogre&t=intranet-Z). :

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

## Отображаем служебную информацию при входе

Очень полезно при входе в систему (не только по SSH) отображать существенную служебную информацию. Для этого надо в папку `/etc/profile.d/` добавить скрипт, который будет исполнятся при каждом входе. Открываем файл на редактирование (нужны права администратора):

```bash
sudo nano /etc/profile.d/sshinfo.sh
```

Помещаем в него, например, следующий скрипт:

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
  echo -e "${LinesPrefix}${b}Загрузка:${n}\t${SystemLoad}\t\t\t${LinesPrefix}${b}Процессов:${n}\t\t${ProcessesCount}";
fi;

if [ ! -z "${MountPointFreeSpace[0]}" ] && [ ! -z "${MountPointFreeSpace[1]}" ]; then
  echo -ne "${LinesPrefix}${b}Диск $SystemMountPoint:${n}\t${MountPointFreeSpace[0]} из ${MountPointFreeSpace[1]}\t\t";
fi;
echo -e "${LinesPrefix}${b}Пользователей онлайн:${n}\t${UsersOnlineCount}";

if [ ! -z "${UsedRAMsize}" ]; then
  echo -ne "${LinesPrefix}${b}Памяти:${n}\t${UsedRAMsize}%\t\t\t";
fi;
echo -e "${LinesPrefix}${b}Непрерывно работает:${n}\t${SystemUptime}";
```

Сохраняем `Ctrl+O` и `Enter`, и выходим из редактора `Ctrl+X`. Затем надо дать право на запуск этого скрипта (нужны права администратора):

```bash
sudo chmod +x /etc/profile.d/sshinfo.sh
```

## Раскрашиваем оболочку bash

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

Всё! Перелогиниваемся чтобы настройки подействовали.

```bash
logout
```


---------------

## Следующие этапы:

* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.
* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) -- доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).
