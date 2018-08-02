# aws-lightsail
Instace who runs catalog app from udacity projects, builds in pyhton using Flask


# Instance info

* IP: 52.28.149.11
* Port: 2200
* User: grader

# Website

http://52.28.149.11.xip.io/

# Summary of software installed

Update and upgrade all packages
```
sudo apt update     # update available package lists
sudo apt upgrade    # upgrade installed packages
```

create **grader** user with no password.
```
sudo adduser grader --disabled-password
sudo vim /etc/sudoers
```

Give root privileges:
```
grader  ALL=(ALL:ALL) ALL
```

Create ssh folder and files to access to the instance from the terminal
```
mkdir .ssh
chmod 700 .ssh
touch .ssh/authorized_keys
chmod 600 .ssh/authorized_keys
```

Edit ssh port to 2200:
```
sudo vim /etc/ssh/sshd_config
```

Allow traffic from http requests, ntp, ssh
```
sudo ufw allow ntp
sudo ufw allow www
sudo ufw allow 2200/tcp
```

Install apache server to handle requests, mod_wsgi, postgresql and git.
```
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi
sudo apt-get install postgresql
sudo apt-get install git
sudo a2enmod wsgi
```

create the app folder and download the repo: 
```
sudo mkdir /var/www/catalog && cd !$
git clone https://github.com/MarcServat/catalog_app
sudo mv application.py __init__.py
``` 

edit the wgsi:
```
sudo vim /var/www/catalog/catalog.wsgi

!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'secret_key'
```

Create apache config file:
```
sudo vim /etc/apache2/sites-enabled/catalog.conf

<VirtualHost *:80>
                ServerName 52.28.149.11.xip.io
                ServerAdmin marcservat@gmail.com
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/catalog/venv/lib/python2.7/site-packages
                WSGIProcessGroup catalog
                <Directory /var/www/catalog/catalog/>
                        WSGIApplicationGroup %{GLOBAL}
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalog/catalog/static
                <Directory /var/www/catalog/catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Restart the server:
```
sudo service apache2 restart
```

# Installer dependencies

Create a virtual enviroment and install required deps:
```
pip install virtualenv
cd /var/www/catalog/catalog/
sudo python -m virtualenv venv
source venv/bin/activate
sudo venv/bin/pip install Flask
sudo venv/bin/pip install -t lib google-api-python-client
sudo venv/bin/pip install requests
sudo venv/bin/pip install --upgrade oauth2client
sudo venv/bin/pip install -t lib google-api-python-client
sudo venv/bin/pip install requirements.txt 
sudo venv/bin/pip install passlib
```