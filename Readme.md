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
