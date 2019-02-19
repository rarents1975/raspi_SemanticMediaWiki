# raspi_SemanticMediaWiki
Installation Description for Sematic Media Wiki on Raspberry Pi

# Installation

## Prerequisites
- Raspberry Pi Model 3 or higher
- Rapbian Jessy

## Create needed folders on the Ubuntu server
**Create folder for video recordings:**  
```
cd
mkdir /Downloads
wget https://releases.wikimedia.org/mediawiki/1.30/mediawiki-1.30.0.tar.gz
```

**Make user www-data and group www-data owner of the folder:**  
```
sudo chown www-data:www-data /video_recordings/mbrdi
```

**Create folder for php and html files:**  
```
sudo mkdir /usr/local/nginx/html
