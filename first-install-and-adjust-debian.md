# Установка системы Debian (или аналога) и первичные настройки

![Начинаем установку](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-01.png?raw=true "Начинаем установку")

2

![Продолжаем установку 2](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-02.png?raw=true "Продолжаем установку 2")
3

![Продолжаем установку 3](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-03.png?raw=true "Продолжаем установку 3")

4

![Продолжаем установку 4](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-04.png?raw=true "Продолжаем установку 4")

5

![Продолжаем установку 5](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-05.png?raw=true "Продолжаем установку 5")

6

![Продолжаем установку 6](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-06.png?raw=true "Продолжаем установку 6")

7

![Продолжаем установку 7](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-07.png?raw=true "Продолжаем установку 7")

8

![Продолжаем установку 8](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-08.png?raw=true "Продолжаем установку 8")

9

![Продолжаем установку 9](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-09.png?raw=true "Продолжаем установку 9")

10

![Продолжаем установку 10](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-10.png?raw=true "Продолжаем установку 10")

11

![Продолжаем установку 11](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-11.png?raw=true "Продолжаем установку 11")

12

![Продолжаем установку 12](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-12.png?raw=true "Продолжаем установку 12")

13

![Продолжаем установку 13](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-13.png?raw=true "Продолжаем установку 13")

14

![Продолжаем установку 14](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-14.png?raw=true "Продолжаем установку 14")

ip addr

![Получаем наш IP с помощью команды ip addr](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-get-ip.png?raw=true "ip addr")

## Устанавливаем sudo

Для установки пакетов и доступа к системным конфигурационным файлам необходимы системные права `root`. Временно перейти к работе из под `root` можно командой `su`, а чтобы выйти из под 'root' в нашего обычного прользователя **[user]]** командой `exit`. Но постоянно прыгать из 'root' в **[user]** неудобно. Можно забыть выйти из под `root` и создать файлы или папки которые станут неоступны **[user]**. Для удобства и однозначного понимания какие команлды отдаются для `root` а какие для **[user]** существует пакет 'sudo'. Перейдем к работе из пол `root` и установим его:

```bash
su
apt-get install sudo
```
Пока мы еще работаем под `root` нужно изменить правила, используемые *sudo* для принятия решения о предоставлении доступа. Они описаны с сисемнойм конфигурационнм файле `/etc/sudoers`. Открываем его на редактирование с помощью редактора *nano*:
```bash
nano /etc/sudoers
```
Чтобы дать пользователю полный доступ к команде `sudo` необходимо в конфигурационный файл после строки `root    ALL=(ALL:ALL) ALL` добавить строку (не забываем менять значения для **[user]** на имея нашего пользователя):
```bash
[user]        ALL=(ALL)       ALL
```
Настройки закончены. Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора nano `Ctrl+X`.

Мы все еще работаем из-под `root`. Перезагружаем машину, чтобы настройки вступили в силу:
```bash
reboot
```

