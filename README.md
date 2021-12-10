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

# Create user and make permission
For give sudo permission to user
```
usermod -g 0 -o niderman
```
May be also this
```
usermod -G root niderman
```

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
Password generator: http://www.onlinepasswordgenerator.ru/ (Chouse all, 20 simbols) 

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

## 🙎Create user in DB
```
sudo mysql
```
```
CREATE USER 'USER_LOGIN'@'localhost' IDENTIFIED BY 'USER_PASSWORD';
GRANT ALL PRIVILEGES ON *.* TO 'USER_LOGIN'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

ctrl+z
```

## ✨Instal GIT
```
sudo apt install git
```

## Node.js
Version in the link can be old! See last version here https://github.com/nvm-sh/nvm
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm list-remote
nvm install v13.6.0 (chose LTS version)
```

# 🌏 Link to domen
```
mkdir /var/www/domain.com
sudo chown -R $USER:$USER /var/www/doman.com/
sudo chmod -R 755 /var/www

sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/YOUR_DOMAIN.conf
sudo nano /etc/apache2/sites-available/YOUR_DOMAIN.conf
```
Change two line, add to line, it shoud seems like this
```
        ServerAdmin YOUR_MAIL
        ServerName YOUR_DOMAIN
        ServerAlias www.YOUR_DOMAIN
        DocumentRoot /var/www/YOUR_DOMAIN/public/
        <Directory /var/www/YOUR_DOMAIN/>
                AllowOverride All
        </Directory>
```
Enable this
```
 sudo a2ensite YOUR_DOMAIN
 sudo a2dissite 000-default.conf
 sudo systemctl restart apache2
```

# 🪐 Setup Laravel
Give permissions
```
sudo chown -R $USER:www-data storage
sudo chown -R $USER:www-data bootstrap/cache
sudo chmod -R ug+w .;
sudo chown -R $(whoami) .git/
```
Settings
```
sudo a2enmod rewrite
```

# ❗❓⚠️ Fix errors
## 🐘Reinstal php
```
sudo apt-get --purge remove php-common
sudo apt-get install php-common php-mysql php-cli
```
## 🐘Setting php
```
sudo nano -H /etc/php/7.4/cli/php.ini
```
```
sudo service apache2 restart
```
## ПReinstal phpMyAdmin
```
sudo dpkg-reconfigure phpmyadmin
```
## Delete PhpMyAdmin
```
sudo dpkg -P phpmyadmin
sudo rm -f /etc/apache2/conf.d/phpmyadmin.conf
sudo service apache2 restart
```
If it isn't work
```
sudo mv /var/lib/dpkg/info/phpmyadmin. /tmp/
sudo dpkg –remove –force-remove-reinstreq phpmyadmin
sudo apt remove phpmyadmin
sudo apt autoremove
sudo apt autoclean
```
If it isn't work too see Remove Broke Package

## If phpMyAdmin not avalible by url
```
sudo nano /etc/apache2/apache2.conf
```
Add line
```
Include /etc/phpmyadmin/apache.conf
```
After that
```
sudo service apache2 restart
```

## УKill process
```
ps aux | grep -i apt
```
See process id here
And kill it
```
sudo kill -9 9425
```

## Remove Broke Package
```
sudo mv /var/lib/dpkg/info/<packagename>.* /tmp/
sudo dpkg --remove --force-remove-reinstreq <packagename>
sudo apt-get remove <packagename>
sudo apt-get autoremove && sudo apt-get autoclean
```

## Delete MySQL (WARNING!! DON'T DO IT WITHOUT VERY STRONG REASON)
```
sudo apt-get remove --purge mysql* -y
sudo apt-get autoremove -y
sudo apt-get autoclean
```
After delete derectory with configs:
```
sudo rm -rf /etc/mysql
```
After that find all fils what wasn't delete:
```
sudo find / -iname 'mysql' -exec rm -rf {} ;
```

# Something else 

## Setup ssh в windows
In powerShell
```
Set-Service ssh-agent -StartupType Manual
```
In cmd
```
ssh-agent
ssh-add .ssh/id_rsa_newizze
```

## INstruction for Debian:
https://www.itzgeek.com/how-tos/linux/debian/how-to-install-laravel-on-debian-11-debian-10.html
https://serverspace.io/support/help/how-to-install-mysql-on-debian-10/

# Make bash script Bash 
Simple bash file looks like this
example.sh
```
#!/bin/sh  
cd /var/www/staging.stubtools.com
git pull
yarn dev
```
Use 
```
sh example.sh
```
for start it
