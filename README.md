# raspi_SemanticMediaWiki
Installation Description for Sematic Media Wiki on Raspberry Pi

# Installation

## Prerequisites
- Raspberry Pi Model 3 or higher
- Rapbian Jessy

## Update the software on your Raspi
```
sudo apt-get update
sudo apt-get upgrade
```

## Install Media Wiki
**Get and unpack the Media Wiki:**  
```
cd
cd Downloads
wget https://releases.wikimedia.org/mediawiki/1.30/mediawiki-1.30.0.tar.gz
tar -xvzf mediawiki-1.30.0.tar.gz
sudo mkdir /var/lib/mediawiki
sudo mv mediawiki-*/* /var/lib/mediawiki
cd /var/www/html
sudo ln -s /var/lib/mediawiki mediawiki
```

**Install base packages required for installation:**  
```
sudo apt-get install apache2 mysql-server php php-mysql libapache2-mod-php php-xml php-mbstring
sudo apt-get install imagemagick
```

**Configuration of MySQL:**  
```
sudo mysqladmin -u root password "enter the new password here"
```
example:
```
sudo mysqladmin -u root password tibet2000
```
Create a database for our MediaWiki installation to use which will be named ‘wiki’. You can use any other name you wish
```
sudo mysql -u root -p
Enter password:
mysql> CREATE DATABASE wiki;
mysql> USE wiki;
```
```
CREATE USER 'wiki'@'localhost' IDENTIFIED BY 'password';
```



