#Udacity Linux Server Configuration
Final project for Udacity Fullstack Developer nanodegree
Working website deployed at http://35.166.113.254


##Access the server using ssh
1.Download Private Key below

2.Move the private key file into the folder ~/.ssh : `mv ~/Downloads/udacity_key.rsa ~/.ssh/`

3.Open your terminal and run following commands

4.`chmod 600 ~/.ssh/udacity_key.rsa`

5.`ssh -i ~/.ssh/udacity_key.rsa root@35.166.113.254`

##Create  new user 'grader'
1. `sudo adduser grader`
2. `sudo nano /etc/sudoers.d/grader`
3. Add following lines `grader ALL=(ALL:ALL) NOPASSWD:ALL`
4. Save and exit

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


