# Setup-laravel-on-ubuntu

## 🔗 Подключение по ssh
ssh -i ПУТЬ_К_ПРИВАТ_КЛЮЧУ ПОЛЬЗОВАТЕЛЬ@СЕРВЕР

## ⚙️Преднастройки 
Обновляем apt
```
sudo apt-get update
sudo apt-get upgrade
```
Проверяем, что достаточно памяти
```
free -m
```
Если памяти мало, то надо поставить swap
```
sudo dd if=/dev/zero of=/swap.file bs=1M count=2048
sudo chmod 600 /swap.file
sudo mkswap /swap.file 
sudo nano /etc/fstab
```
Добавляем строку
```
/swap.file      swap            swap    defaults        0       0
```
Перезагружаем
```
reboot now
```

## 🖥️ Установка Apache
Устанавливаем apache
```
sudo apt-get install apache2
```
Проверяем, что apache успешно установился
```
service apache2 status
```
## 📝 Установка MySQL
```
sudo apt-get install mysql-server
```

Защита MySQL
```
sudo mysql_secure_installation
пароль
```
Установить валидацию паролья - n, остальное - y
генератор паролей: http://www.onlinepasswordgenerator.ru/ (выбрать все галочки, 20 символов) 

## 🐘 Установка PHP
```
sudo apt-get install php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
```

Проверка PHP
```
php -v
```

## 🤵🐘Установка PhpMyAdmin
```
sudo apt install php-mbstring
sudo apt install phpmyadmin
Выбор: apache, yes, 
```
```
sudo phpenmod mbstring
sudo systemctl restart apache2
```
Будет доступен по адресу
http://ip||domain/phpmyadmin/

## 🙎Создание пользователя
```
sudo mysql
```
*мы должны оказаться внутри mysql>
создаем пользователя и даем ему все права.
```
CREATE USER 'ЛОГИН'@'localhost' IDENTIFIED BY 'ПАРОЛЬ';
GRANT ALL PRIVILEGES ON *.* TO 'ЛОГИН'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

ctrl+z
```

### Защита phpMyAdmin
Добавляем авторизацию
```
sudo vi /usr/share/phpmyadmin/.htaccess
```

Вставляем слtдующий код:
```
AuthType Basic
AuthName "Restricted Files"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user

ctrl+c
shift+z
shift+z
```

Зададим новый пароль для пользователя
```
sudo htpasswd -c /etc/phpmyadmin/.htpasswd ЛОГИН
ПАРОЛЬ
ЕЩЕ РАЗ ПАРОЛЬ
```

Включим настройки
```
sudo vi /etc/apache/apache2.conf
```

Ищем эти строки, и меняем, чтобы было вот так
```
<Directory /usr/share>
AllowOverride All
Require all granted
</Directory>
```

## ✨Устанавливаем остальное
```
sudo apt install git
sudo apt install nodejs
sudo apt install npm
```

# 🌏 Привязка к домену
```
mkdir /var/www/domain.com
sudo chown -R $USER:$USER /var/www/doman.com/
sudo chmod -R 755 /var/www

sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/example.com.conf
sudo nano /etc/apache2/sites-available/example.com.conf
```
Две строчки меняем, две добавляем, чтобы было как
```
        ServerAdmin ПОЧТА
        ServerName ДОМЕН
        ServerAlias www.ДОМЕН
        DocumentRoot /var/www/ДОМЕН/ЕСЛИ_НАДО_ТО_PUBLIC
```
Включаем
```
 sudo a2ensite ДОМЕН
 sudo a2dissite 000-default.conf
 sudo systemctl restart apache2
```

# 🪐 Настройка Laravel
Выдача прав
```
sudo chown -R $USER:www-data storage
sudo chown -R $USER:www-data bootstrap/cache
```
Настройка
```
sudo a2enmod rewrite
```

# ❗❓⚠️ Решние проблем
## 🐘Настройки php
```
sudo nano -H /etc/php/7.4/cli/php.ini
```

## Перенастройка phpMyAdmin
```
sudo dpkg-reconfigure phpmyadmin
```
## Удаление PhpMyAdmin
```
sudo dpkg -P phpmyadmin
sudo rm -f /etc/apache2/conf.d/phpmyadmin.conf
sudo service apache2 restart
```
Если не получилось
```
sudo mv /var/lib/dpkg/info/phpmyadmin. /tmp/
sudo dpkg –remove –force-remove-reinstreq phpmyadmin
sudo apt remove phpmyadmin
sudo apt autoremove
sudo apt autoclean
```
Если не получилось, см. Удаление сломаного пакета

## Если phpMyAdmin не доступен по адресу
```
sudo nano /etc/apache2/apache2.conf
```
Добавляем строку
```
Include /etc/phpmyadmin/apache.conf
```
После
```
sudo service apache2 restart
```

## Убитие процесса
```
ps aux | grep -i apt
```
смотрим тут id процесса
И убиваем его
```
sudo kill -9 9425
```

## Удаление сломанного пакета
```
sudo mv /var/lib/dpkg/info/<packagename>.* /tmp/
sudo dpkg --remove --force-remove-reinstreq <packagename>
sudo apt-get remove <packagename>
sudo apt-get autoremove && sudo apt-get autoclean
```

## Удаление MySQL (ТОЛЬКО ПРИ КРАЙНЕЙ НЕОБХОДИМОСТИ)
```
sudo apt-get remove --purge mysql* -y
sudo apt-get autoremove -y
sudo apt-get autoclean
```
Затем удалите папку с конфигурацией:
```
sudo rm -rf /etc/mysql
```
Ну и затем хардкор: найти все оставшиеся файлы по маске и удалить:
```
sudo find / -iname 'mysql' -exec rm -rf {} ;
```
