# Setup-laravel-on-ubuntu

## Обновляем apt
```
sudo apt-get update
sudo apt-get upgrade
```

## Установка Apache
Устанавливаем apache
```
sudo apt-get install apache2
```
Проверяем, что apache успешно установился
```
service apache2 status
```
## Установка MySQL
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

## Установка PHP
```
sudo apt-get install php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
```

Проверка PHP
```
php -v
```

## Установка PhpMyAdmin
```
sudo apt install php-mbstring
sudo apt install phpmyadmin
Выбор: apache, yes, 
```
```
sudo phpenmod mbstring
sudo systemctl restart apache2
```

## Создание пользователя
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

Защита phpMyAdmin
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

Устанавливаем остальное
```
sudo apt install git
sudo apt install nodejs
sudo apt install npm
```

# Привязка к домену
```
cd /etc/apache2/
sudo nano apache2.conf
```
И вписываем
```
<VirtualHost IP:80>                             # вместо IP указываем IP-адрес VPS
    ServerAdmin webmaster@domain.com            # почтовый адрес администратора
    DocumentRoot /var/www/html/domain_com       # путь расположения папки с файлами сайта
    ServerName domain.com                       # домен, по которому должен открываться сайт
    ErrorLog logs/domain.com-error_log          # путь и имя файла с журналом ошибок
    CustomLog logs/domain.com-access_log common # путь и имя файла с журналом запросов
</VirtualHost>
```

