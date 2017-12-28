# Установка системы Debian (или аналога) и первичные настройки

Для составления последовательности для установки древней PHP 5.2.17 понадобилось перерыть много мирового интернета и совершить много ошибок. Данная последовательность рецептов гарантированно сработает для [Debian 8.6 (Jessie) x64](https://cdimage.debian.org/cdimage/archive/8.6.0/amd64/). К сожалению, автор не смог проверить применимость рецепта для всех вариантов [Debian 8.x «Jessie»](https://www.debian.org/releases/jessie/debian-installer/). Для Debian 9 «Stretch» не хватало ещё большего числа библиотек. Если у вас есть рекомендации, по сборке необходимого состава библиотек для этих и других систем, присылайте коммиты или свяжитесь с автором.

С высокой вероятностью данная инструкция, с небольшими поправками на состав библиотек, сработает и для других версий PHP и систем линейки Debian (например, Ubuntu и её клоны, Raspbian, Noobs и т.д.).

Начинаем. Выбираем `Install`:

![Начинаем установку](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-01.png?raw=true "Начинаем установку")

Выбираем локализацию `Russian - Русский`:
![Продолжаем установку 2](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-02.png?raw=true "Продолжаем установку 2")

Выбираем регион `Российская Федерация`:
![Продолжаем установку 3](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-03.png?raw=true "Продолжаем установку 3")

Указываем раскладку клавиатуры `Русская`:
![Продолжаем установку 4](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-04.png?raw=true "Продолжаем установку 4")

Выбираем способ переключения клавиатуры:
![Продолжаем установку 5](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-05.png?raw=true "Продолжаем установку 5")

Происходит загрузка дополнительных компанентов:
![Продолжаем установку 6](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-06.png?raw=true "Продолжаем установку 6")

Указываем host-имя сервера:
![Продолжаем установку 7](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-07.png?raw=true "Продолжаем установку 7")

Указываем имя домена (то, что справа от полного имени хоста):
![Продолжаем установку 8](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-08.png?raw=true "Продолжаем установку 8")

Вводим пароль **root**-пользователя:
![Продолжаем установку 9](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-09.png?raw=true "Продолжаем установку 9")

Повторяем пароль **root**:
![Продолжаем установку 10](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-10.png?raw=true "Продолжаем установку 10")

Вводим имя пользователя-администратора:
![Продолжаем установку 11](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-11.png?raw=true "Продолжаем установку 11")

Теперь логин администратора:
![Продолжаем установку 12](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-12.png?raw=true "Продолжаем установку 12")

Пароль администратора:
![Продолжаем установку 13](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-13.png?raw=true "Продолжаем установку 13")

Повторяем пароль:
![Продолжаем установку 14](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-14.png?raw=true "Продолжаем установку 14")

15

![Продолжаем установку 15](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-15.png?raw=true "Продолжаем установку 15")

16

![Продолжаем установку 16](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-16.png?raw=true "Продолжаем установку 16")

17

![Продолжаем установку 17](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-17.png?raw=true "Продолжаем установку 17")

18

![Продолжаем установку 18](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-18.png?raw=true "Продолжаем установку 18")

19

![Продолжаем установку 19](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-19.png?raw=true "Продолжаем установку 19")

20

![Продолжаем установку 20](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-20.png?raw=true "Продолжаем установку 20")

21

![Продолжаем установку 21](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-11.png?raw=true "Продолжаем установку 21")

22

![Продолжаем установку 22](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-22.png?raw=true "Продолжаем установку 22")

23

![Продолжаем установку 23](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-23.png?raw=true "Продолжаем установку 23")

24

![Продолжаем установку 24](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-24.png?raw=true "Продолжаем установку 24")

25

![Продолжаем установку 25](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-25.png?raw=true "Продолжаем установку 25")

26

![Продолжаем установку 26](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-26.png?raw=true "Продолжаем установку 26")

27

![Продолжаем установку 27](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-27.png?raw=true "Продолжаем установку 27")

28

![Продолжаем установку 28](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-28.png?raw=true "Продолжаем установку 28")

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

