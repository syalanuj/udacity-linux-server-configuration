#Udacity Linux Server Configuration
Final project for Udacity Fullstack Developer nanodegree
Working website deployed at http://35.166.113.254

'grader' password 'Passw0rd'

##Access the server using ssh
1.Download Private Key below

2.Move the private key file into the folder ~/.ssh : `mv ~/Downloads/udacity_key.rsa ~/.ssh/`

3.Open your terminal and run following commands

4.`chmod 600 ~/.ssh/udacity_key.rsa`

5.`ssh -i ~/.ssh/udacity_key.rsa root@35.166.113.254`

##Create  new user 'grader'
1. `sudo adduser grader`
2. `sudo nano /etc/sudoers.d/grader`
3. Add following lines `grader ALL=(ALL:ALL) ALL`
4. Save and exit

### ssh login using user 'grader'
1. Generate public and private keys using `ssh-keygen` and save the key as "authorized_keys"
2. Move the private key in ~/.ssh
3. On VM copy the public key to

```
$ su - grader
$ mkdir .ssh
$ sudo nano .ssh/authorized_keys
```

4. Copy public key to this file

5. Give following permissions
```
$ chmod 700 .ssh
$ chmod 644 .ssh/authorized_keys
```

 Restart ssh service `ssh service restart`

Login using grader `ssh -i [privateKeyFilename] grader@35.166.113.254`. Use the private key attached with "Passw0rd" as password


##Update all currently installed packages
`sudo apt-get update`
`sudo apt-get upgrade`

##Change ssh port to 2200
1. `sudo nano /etc/ssh/sshd_config` search for port 22 and change to 2200
2. Restart ssh service `sudo service ssh restart`

##Configure the Uncomplicated Firewall (UFW)
###only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
  ```
    sudo ufw allow 2200/tcp
    sudo ufw allow 80/tcp
    sudo ufw allow 123/udp
    sudo ufw enable 
  ```
  
##Configure the local timezone to UTC
Use `sudo dpkg-reconfigure tzdata` to set timezone to utc

##Install and configure Apache to serve a Python mod_wsgi application
Reference taken from https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-apache-and-mod_wsgi-on-ubuntu-14-04

1. Install Apache2 `sudo apt-get install apache2`
2. Install mod_wsgi `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. Restart service `sudo service apache2 restart`

##Install and configure PostgreSQL:
####Do not allow remote connections
####Create a new user named catalog that has limited permissions to your catalog application database

1. `sudo apt-get install postgresql`
2. Check for remote connections `sudo nano /etc/postgresql/9.3/main/pg_hba.conf`
3. `sudo su - postgres` Login as "postgres"
4. Type `psql` 
5. Create new database named "catalog": `CREATE DATABASE catalog;`
6. Create new user "catalog": `CREATE USER catalog;`
7. Set password for user "catalog": `ALTER ROLE catalog WITH PASSWORD 'password';`
8. Give user "catalog" permission to "catalog" application database: 
9. quit from postgres

##Setup Item catalog app
1. Install git `sudo apt-get install git`
2. Make directory for application `cd /var/www/` and `sudo mkdir ItemCatalog`
3. `cd ItemCatalog`
4. Clone application `git clone https://github.com/syalanuj/ItemCatalog.git`
5. `cd ItemCatalog/vagrant`
6. Move "catalog" (application folder to "/var/www") : `sudo mv catalog ../../catalog`
7. go to application folder `cd catalog`
8. Rename "project.py" to "__init__.py" `sudo mv project.py __init__.py`
9. Edit "__init__.py" and "database_setup.py" replace 'sqlite:///itemcatalog.db' with 'postgresql://catalog:password@localhost/catalog' in both the files
10. Install pip `sudo apt-get install python-pip`
11. Install psycopg2 `sudo apt-get -qqy install postgresql python-psycopg2`
12. Create database schema `sudo python database_setup.py`
13. Create catalog.conf `sudo nano /etc/apache2/sites-available/catalog.conf`
14. Add

```
<VirtualHost *:80>
    ServerName 35.166.113.254
    ServerAdmin syal.anuj@gmail.com
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/static
    <Directory /var/www/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

15. Enable virtual host `sudo a2ensite FlaskApp`
16. Create .wsgi file `sudo nano /var/www/catalog/catalog.wsgi`
17. Add following
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'
```

18. Restart apache service `sudo service apache2 restart`

##Remove Remote login using root 
1. `sudo nano /etc/ssh/sshd_config`
2. change PermitRootLogin without-password to PermitRootLogin no.
3. save and `sudo service ssh restart`
