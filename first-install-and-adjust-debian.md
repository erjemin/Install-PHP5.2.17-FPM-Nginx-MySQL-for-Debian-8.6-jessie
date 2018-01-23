### Развертывание современного веб-сервера под старые проекты на базе PHP 5.2.17

# 1: Установка системы Debian (или аналога) и первичные настройки

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

Происходит загрузка дополнительных компонентов:

![Продолжаем установку 6](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-06.png?raw=true "Продолжаем установку 6")

Указываем host-имя сервера:

![Продолжаем установку 7](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-07.png?raw=true "Продолжаем установку 7")

Указываем имя домена (то, что справа от полного имени хоста):

![Продолжаем установку 8](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-08.png?raw=true "Продолжаем установку 8")

Вводим пароль **root**-пользователя **[root-pwd]**:

![Продолжаем установку 9](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-09.png?raw=true "Продолжаем установку 9")

Повторяем пароль  **[root-pwd]**:

![Продолжаем установку 10](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-10.png?raw=true "Продолжаем установку 10")

Вводим имя пользователя-администратора:

![Продолжаем установку 11](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-11.png?raw=true "Продолжаем установку 11")

Теперь логин администратора **[user]**:

![Продолжаем установку 12](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-12.png?raw=true "Продолжаем установку 12")

Пароль администратора **[user_pwd]**:

![Продолжаем установку 13](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-13.png?raw=true "Продолжаем установку 13")

Повторяем пароль **[user_pwd]**:

![Продолжаем установку 14](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-14.png?raw=true "Продолжаем установку 14")

Указываем часовой пояс:

![Продолжаем установку 15](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-15.png?raw=true "Продолжаем установку 15")

Метод разметки диска:

![Продолжаем установку 16](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-16.png?raw=true "Продолжаем установку 16")

Указываем диск, который будет размечен:

![Продолжаем установку 17](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-17.png?raw=true "Продолжаем установку 17")

Выбираем схему разметки по партициям:

![Продолжаем установку 18](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-18.png?raw=true "Продолжаем установку 18")

Фиксируем разметку:

![Продолжаем установку 19](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-19.png?raw=true "Продолжаем установку 19")

Подтверждаем:

![Продолжаем установку 20](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-20.png?raw=true "Продолжаем установку 20")

Ещё раз:

![Продолжаем установку 21](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-21.png?raw=true "Продолжаем установку 21")

Соглашаемся, что мы готовы получать обновления из сети:

![Продолжаем установку 22](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-22.png?raw=true "Продолжаем установку 22")

Выбираем, географическое расположение репозиториев для получения обновлений `Российская Федерация`:

![Продолжаем установку 23](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-23.png?raw=true "Продолжаем установку 23")

...и что особую любовь мы испытываем к репозиториям Яндекс `mirror.yandex.ru`:

![Продолжаем установку 24](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-24.png?raw=true "Продолжаем установку 24")

Если для выхода в интернет мы используем proxy, укажем их тут:

![Продолжаем установку 25](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-25.png?raw=true "Продолжаем установку 25")

Из архива качается и устанавливается куча пакетов:

![Продолжаем установку 26](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-26.png?raw=true "Продолжаем установку 26")

Устанавливаем загрузчик:

![Продолжаем установку 28](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-28.png?raw=true "Продолжаем установку 28")

Теперь система готова и следует перезагрузится:

![Продолжаем установку 27](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-debian-install-27.png?raw=true "Продолжаем установку 27")

## Базовая подготовка системы

Если наш сервер получает IP-адреса через DHCP нужно узнать его IP. Логируемся в систему и даём команду `ip addr`:

![Получаем наш IP с помощью команды ip addr](https://github.com/erjemin/Install-PHP5.2.17-FPM-Nginx-MySQL-for-Debian-8.6-jessie/blob/master/img/Z-get-ip.png?raw=true "ip addr")

Теперь мы знаем IP нашего сервера, и по нему уже открыт SSH-доступ. Можем логироваться через любой SSH-клиент (рекомендую [Xshell](https://www.netsarang.com/products/xsh_overview.html) и переходить к [расширенным настройкам SSH и среды](ssh-tuning.md). Но перед этим, целесообразно сделать установку некоторых полезных пакетов и обновиться:

### Устанавливаем sudo

Для установки пакетов и доступа к системным конфигурационным файлам необходимы системные права `root`. Временно перейти к работе из под `root` можно командой `su`, а чтобы выйти из под 'root' в нашего обычного пользователя **[user]** — командой `exit`. Постоянно прыгать из 'root' в **[user]** неудобно. Можно забыть выйти из под `root` и создать файлы или папки которые станут недоступны **[user]**. Для удобства и однозначного понимания какие команды отдаются для `root` а какие для **[user]** существует пакет 'sudo'. Перейдём к работе из под `root` и установим его:

```bash
su
```
Вводим **[root-pwd]**, а затем:
```bash
apt-get install sudo
```
Пока мы ещё работаем под `root` нужно изменить правила, используемые *sudo* для принятия решения о предоставлении доступа пользователям. Они описаны в системном конфигурационном файле `/etc/sudoers`. Открываем его на редактирование с помощью редактора *nano*:
```bash
nano /etc/sudoers
```
Дать пользователю полный доступ нашему пользователю к команде `sudo` необходимо визменив конфигурационный файл. После строки `root    ALL=(ALL:ALL) ALL` добавим следующую строку (не забываем менять значения для **[user]** на имея нашего пользователя):
```bash
[user]        ALL=(ALL)       ALL
```
Настройки закончены. Сохраняем файл `Ctrl+O` и `Enter`, а затем выходим из редактора nano `Ctrl+X`.

### Установка дополнительных пактов

Мы всё ещё работаем из-под `root`. Устанавливаем файл-менеджер `mc`:
```bash
apt-get install mc
```
Устанавливаем продвинутый системный монитор `htop`:
```bash
apt-get install htop
```
Устанавливаем службу точного времени `nlp`:
```bash
apt-get install ntp
```
Устанавливаем `LDAP` — службу поддержки Microsoaft Active Directory:
 ```bash
apt-get install ldap-utils
```

### Обновляем пекты системы

Сперва нужно обновить список доступных пакетов репозиториев:
```bash
apt-get update
```
А замем получить и применить все доступные к обновлению пакеты:
```bash
apt-get upgrade
```

## Сервер готов
Перезагружаем машину, чтобы настройки **sudo** вступили в силу:
```bash
reboot
```


---------------------

## Переходим к дельнейшим этапам:

* 2. [Настройка SSH и окружения клиента](ssh-tuning.md).
* 3. [Установка и настройка MySQL](install-and-adjust-MySQL-fоr-php-5.2.17.md).
* 4. [Сборка и настройка допотопного PHP 5.2.17 вместе с FPM](make-php-5.2.17-for-debian-jessie.md).
* 5. [Установка и настройка веб-сервера nginx](install-and-adjust-nginx-fоr-php-5.2.17.md).
* 6. [Настройка Joomla! 1.0.15](settings-config-for-Joolma-1.0.15.md) и настройка phpMyAdmin.
* 7. [Доустанавливаем поддержку LDAP](adding-ldap-to-debian-nginx-and-php.md) — доступ к Active Directory.
* 8. [Чистим систему от лишних модулей](claen.md).
* 9. [Резервный сайт, резервное копирование и восстановление сайта и синхронизация](backup-restore-and-syncronization-sites.md).

