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
sudo apt-get install php7.0-apcu -y
sudo apt-get install php-intl
sudo apt-get install php7.0-gd
sudo apt-get install imagemagick
```

To ensure that APC-Cache is enabled, edit /etc/php/7.0/apache2/php.ini and change:

```
sudo nano /etc/php/7.0/apache2/php.ini
```
Change
```
;opcache.enable=0
```
to
```
;opcache.enable=1
```
Next, make sure the php mod is enabled:
```
phpenmod opcache
```
Finally restart Apache:
```
sudo service apache2 restart
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
example:
```
CREATE USER 'wiki'@'localhost' IDENTIFIED BY 'tibet2000';
```
```
GRANT ALL PRIVILEGES ON wiki.* TO 'wiki'@'localhost';
FLUSH PRIVILEGES;
quit
```

**Configuration of Media Wiki:**  
Goto your browser and navigate to the following site:
```
http://ip_address_of_pi3/mediawiki
```
If you are getting a page that says ‘LocalSettings.php not found’ then click on the ‘setup the wiki first’. If you do not get that page, then you skipped a step somewhere and would need to start over.

The next few screens you will need to go over everything carefully. You can leave all of the settings as their defaults. But when you get to the database connection settings, use the same settings as in the creation of the MYSQL database which was done in the MYSQL shell moments earlier.

ON the page where it asks for a name for the wiki, you can name the wiki whatever you want it to be. For testing purposes I will call it temporary wiki. It will also ask for a username and password (last time it is asking for username’s and passwords I promise). You can even put in the email address so that it can send out emails for security purposes (and how to do that will be in a follow-up tutorial).

When you are on the last screen, click on ‘Continue’ and then leave it until another page loads asking to download the file named ‘LocalSettings.php’ save this file somewhere you can access it.

You will now save the LocalSettings.php file on /var/www/html/mediawiki
Easiest is to download the file on your Win PC, open it and past it into the terminal of the Raspi:
```
sudo nano /var/www/html/mediawiki/LocalSettings.php
```

## Install Semantic Media Wiki extension
**Install Composer:**  
```
cd
mkdir composer
cd composer
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php --filename=composer
sudo mv composer /usr/local/bin/composer
```

**Create composer config file to install Semantic Media Wiki extension**
```
cd /home/pi/composer
nano composer.local.json
```
Paste the following configuration into the json file:
```
{
    "require": {
        "mediawiki/semantic-media-wiki": "~3.0"
    }
}
```
Run the following "initialization" command from the base directory of your MediaWiki installation:
```
cd /var/www/html/mediawiki
sudo composer require mediawiki/semantic-media-wiki "~3.0" --update-no-dev
```
Run the setup script from the base directory of your MediaWiki installation:
```
cd /var/www/html/mediawiki
php maintenance/update.php
```
Add --skip-external-dependencies to the call of the setup script if you get an error message similar to the following one:
```
Error: your composer.lock file is not up to date. Run "composer update --no-dev" to install newer dependencies
```
```
cd /var/www/html/mediawiki
php maintenance/update.php --skip-external-dependencies
```

**Enable Semantic MediaWiki:**  
Add a call to enableSemantics() to the end of the "LocalSettings.php" file. enableSemantics() takes in the domain name of the wiki; a wiki located at "example.org", for instance, should have the following call:
```
enableSemantics( 'example.org' );
```

```
cd /var/html/www/mediawiki
sudo nano LocalSettings.php
```

At the end of the file add:
```
enableSemantics( 'servername.org' );
```

Goto your browser and refresh the homepage of Media Wiki: http://ip_address_of_pi3/mediawiki

An error will appear. Go back to the unix shell and execute the update script again:

```
cd /var/html/www/mediawiki
php maintenance/update.php --skip-external-dependencies
```

Goto your browser and refresh the homepage of Media Wiki: http://ip_address_of_pi3/mediawiki
If on the lower right corner the Sematic Media Wiki logo will appear, your installation most probably was successful. 

## Install Extensions
**Install PageForms Extension:**  

Some general informations howto install extensions can be found here:
https://www.mediawiki.org/wiki/Manual:Extensions/de#Installieren_einer_Erweiterung

First download extension:  
Goto Extension Distributor under https://www.mediawiki.org/wiki/Special:ExtensionDistributor 
and search for PageForms. 

Download the tar archive and unzip it within the extension folder of your media wiki installation:
```
cd /var/www/html/mediawiki/extensions
sudo tar xzf PageForms-REL1_30-3e86843.tar.gz
```
Take care that the new folder belongs to user www-data or under whatever user your Apache web server is running.

Then edit LocalSettings.php in the main folder of media wiki.

```
cd /var/www/html/mediawiki
sudo nano LocalSettings.php
```

Add the follwing lines at the end of the php file:
```
if ( !$wgCommandLineMode ) {
   wfLoadExtension ( 'PageForms' );
}
```
Check if everything went on well by loading a form extension:
http://yourwebserver/mediawiki/index.php/Special:CreateForm

A form with the headline "Create a form" should appear.

You can get some informations about the PageForms extension here:
https://www.mediawiki.org/wiki/Extension:Page_Forms/Special_pages

## Install and configure Apache Jena Fuseki (RDF Triple Store)
**Install Apache Jena Fuseki:**  
```
sudo adduser --disabled-password fuseki
cd /home/fuseki
sudo -u fuseki bash
wget http://archive.apache.org/dist/jena/binaries/apache-jena-fuseki-2.6.0.tar.gz
tar xzf apache-jena-fuseki-2.4.1.tar.gz
ln -s apache-jena-fuseki-2.4.1 fuseki
cd fuseki
```

Fuseki will in `/home/fuseki/fuseki`. Call `./fuseki-server` for testing.

**Setup Fuseki as service**  
```
sudo vim /etc/default/fuseki # edit file
cat /etc/default/fuseki
FUSEKI_HOME=/home/fuseki/fuseki
FUSEKI_BASE=/etc/fuseki
sudo mkdir /etc/fuseki
sudo chown fuseki /etc/fuseki
sudo cp /home/fuseki/fuseki/fuseki /etc/init.d/
sudo update-rc.d fuseki defaults
```

**Setup Semantic Mediawiki to use with Fuseki**  
For details how to configure Semantic Media Wiki for Apache Jena Fuseki see also 
https://www.semantic-mediawiki.org/wiki/Help:SPARQLStore/RepositoryConnector/Fuseki
