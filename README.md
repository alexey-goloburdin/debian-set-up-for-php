# Debian Server Set Up for LAMP Server (Apache, PHP, MariaDB)

In this guide we will set up clean Debian server for PHP projects. We will configure secure SSH connection, install from Debian repositories and from sources all needed packages and ware it together for working PHP projects.

[Youtube video guide (in Russian)](https://www.youtube.com/watch?v=LvvSlljb8Yw)

## Setup SSH

```
sudo apt-get update ; \
sudo apt-get install -y vim mosh tmux htop git curl wget unzip zip gcc build-essential make
```

Configure SSH:

```
sudo vim /etc/ssh/sshd_config
    AllowUsers www
    PermitRootLogin no
    PasswordAuthentication no
```

Restart SSH server, change `www` user password:

```
sudo service ssh restart
sudo passwd www
```

## ZSH

```
sudo apt-get install -y zsh

sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## MariaDB

```
sudo apt install -y mariadb-server mariadb-client
sudo mysql_secure_installation
```

Login with `root` user is available just with `sudo`:

```
sudo mariadb -u root
```

So, create database and user and give him privileges to new database:

```
sudo mariadb -u root
CREATE DATABASE new_db_name COLLATE 'utf8_general_ci';
CREATE USER new_db_user IDENTIFIED BY 'some_password';
GRANT ALL privileges ON new_db_name .* TO new_db_user;
```

## Apache, PHP

Install Apache, create `code` directory in user home directory:

```
sudo apt install -y apache2 apache2-utils
cd
mkdir code
cd code
mkdir newproject
sudo chown www-data:www-data /home/www/code/ -R
```

Now install PHP:

```
sudo apt install -y php7.0 libapache2-mod-php7.0 php7.0-mysql php-common php7.0-cli php7.0-common php7.0-json php7.0-opcache php7.0-readline php7.0-mbstring php7.0-xml php7.0-gd
```

Enable `mod_php` in Apache:

```
sudo a2enmod php7.0
sudo systemctl restart apache2
```

Configure Apache:

```
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/newproject.conf

sudo vim /etc/apache2/sites-available/newproject.conf
```

Change or append lines:

```
        DocumentRoot /home/www/code/newproject
        ServerName newproject.your-site.ru
```

Enable site config in Apache, enable `mod_rewrite`:

```
sudo a2ensite newproject.conf

sudo a2enmod rewrite
```

Configure Apache for `.htaccess` files:

```
sudo vim /etc/apache2/apache2.conf
```

Change lines:

```
<Directory />
    Options FollowSymLinks
    AllowOverride All
    Order allow,deny
    Allow from all
</Directory>
```

Restart Apache:

```
sudo service apache2 restart
```


