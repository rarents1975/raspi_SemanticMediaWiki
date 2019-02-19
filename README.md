# raspi_SemanticMediaWiki
Installation Description for Sematic Media Wiki on Raspberry Pi

# Installation

## Prerequisites
- Raspberry Pi Model 3 or higher
- Rapbian Jessy

## Install Media Wiki
**Get and unpack the Media Wiki:**  
```
cd
cd Downloads
wget https://releases.wikimedia.org/mediawiki/1.30/mediawiki-1.30.0.tar.gz
tar -xvzf mediawiki-1.30.0.tar.gz
```

**Install base packages required for installation:**  
```
sudo apt-get install apache2 mysql-server php php-mysql libapache2-mod-php php-xml php-mbstring
sudo apt-get install php-apc imagemagick
```

**Create folder for php and html files:**  
```
sudo mkdir /usr/local/nginx/html
