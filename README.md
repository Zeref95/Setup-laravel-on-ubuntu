# Setup laravel on ubuntu 20.04

More info: https://www.rosehosting.com/blog/install-laravel-on-ubuntu-20-04/
In this gide we setup laravel project in clean vps server.

## 🔗 Connect by ssh
#### If you have ssh key
ssh -i "privat_key" user@server
#### If you have password
ssh user@server
#### If you use VScode for connect
ssh user@server -A

## Pre-install
```
sudo apt-get update && sudo apt-get upgrade
```
Check you have enough RAM
```
free -m
```
If you have less then 2 GB, and haven't swap, you have to add swap
```
sudo dd if=/dev/zero of=/swap.file bs=1M count=2048
sudo chmod 600 /swap.file
sudo mkswap /swap.file 
sudo nano /etc/fstab
```
Add line
```
/swap.file      swap            swap    defaults        0       0
```
Reboot server
```
reboot now
```

## 🖥️ Setup Apache and 🐘 PHP 7.4
```
apt-get install apache2 php7.4 libapache2-mod-php7.4 php7.4-curl php-pear php7.4-gd php7.4-dev php7.4-zip php7.4-mbstring php7.4-mysql php7.4-xml curl -y
systemctl start apache2 && systemctl enable apache2
```
If everything is alright you can go to your ip (domain) and you shoud see apache page
http://ip||domain/
Also you can check php install seccessfuly
```
php -v
```

## 🖥️ Setup Composer
```
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
chmod +x /usr/local/bin/composer
```
Check this out:
```
composer --version
```

## 📝 Setup MySQL
```
sudo apt-get install mysql-server
```
генератор паролей: http://www.onlinepasswordgenerator.ru/ (выбрать все галочки, 20 символов) 

## 🤵🐘Setup PhpMyAdmin
```
sudo apt install phpmyadmin
```
Chouse: apache, yes, new password, password confirmation
```
sudo phpenmod mbstring
sudo systemctl restart apache2
```
```
sudo nano /etc/apache2/apache2.conf
```
Then add the following line somewhere to the file:
```
Include /etc/phpmyadmin/apache.conf
```
Then restart apache:
```
/etc/init.d/apache2 restart
```
Phpmyadmin will alow by url:
http://ip||domain/phpmyadmin/

## 🙎Создание пользователя
```
sudo mysql
```
```
CREATE USER 'ЛОГИН'@'localhost' IDENTIFIED BY 'ПАРОЛЬ';
GRANT ALL PRIVILEGES ON *.* TO 'ЛОГИН'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

ctrl+z
```

## ✨Устанавливаем остальное
```
sudo apt install git
sudo apt install nodejs (найти как ставить более новую версию)
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
        DocumentRoot /var/www/ДОМЕН/public/
        <Directory /var/www/ДОМЕН/>
                AllowOverride All
        </Directory>
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
sudo chmod -R ug+w .;
sudo chown -R $(whoami) .git/
```
Настройка
```
sudo a2enmod rewrite
```

# ❗❓⚠️ Решние проблем
## 🐘Переустановка php
```
sudo apt-get --purge remove php-common
sudo apt-get install php-common php-mysql php-cli
```
## 🐘Настройки php
```
sudo nano -H /etc/php/7.4/cli/php.ini
```
```
sudo service apache2 restart
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

## Установка ssh в windows
В powerShell
```
Set-Service ssh-agent -StartupType Manual
```
В командной строке
```
ssh-agent
ssh-add .ssh/id_rsa_newizze
```
