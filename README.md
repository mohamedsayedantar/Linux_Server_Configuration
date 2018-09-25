# Linux Server Configuration
  this project takes a baseline installation of a Linux server and prepare it to host a web application.
  by securing the server from a number of attack vectors, installing and configuring the database server,
  and deploying the web applications onto it.
  

## 1- the server
  by using Ubuntu Linux server instance on Amazon Lightsail, follow these steps:- 
  1. creat an acount on `https://aws.amazon.com` if you don not have one. 
  2. log into `https://lightsail.aws.amazon.com/ls/webapp/home/instances` . 
  3. click on `create instance` . 
  4. choose `os only`, `ubuntu` . 
  5. choose your instance plan, then give your instance a hostname. 
  6. click create, and wait for the instance to start up. 
  for more information `https://serverpilot.io/docs/how-to-create-a-server-on-amazon-lightsail`. 
    
    
## 2- SSH Connection
  you can click on `Connect using SSH` and use the online terminal,
  or follow these steps to use your own terminal:- 
  1. go down and click on `Account page`, then click on `SSH Keys`. 
  2. go down and click on `Download` your Default Private Key. 
  3. move the file you downloaded to `~/.ssh/` using `mv` command. 
  4. rename this file to be `key.rsa`, or any name but with `.rsa` not `.pem`. 
  5. perform this command `chmod 600 ~/.ssh/lightsail_key.rsa` in your terminal. 
  6. to log into your machine type `ssh ubuntu@18.215.131.65 -i ~/.ssh/key.rsa`, as my ip=`18.215.131.65` and my file name is `key.rsa`. 
  
  
## 3- ubdate :-
  update your software and packages using:-
  
    sudo apt-get update
    sudo apt-get upgrade
    
    
## 4- new user named grader :-
  1. to create a new user type `sudo adduser grader`, add the password for grader, fill the information if you want. 
  2. type `sudo nano /etc/sudoers.d/grader` to add file named grader to make grader able to use sudo command and add `grader ALL=(ALL:ALL) ALL`. 
  3. then change to grader user using `su - grader` and type your password. 
  4. add the `.ssh` file using `mkdir .ssh`. 
  5. using `touch .ssh/authorized_keys` to add the authorized_keys file. 
  6. now copy the key inside key.rsa file and type `nano .ssh/authorized_keys` to add this key to the authorized_keys file. 
  7. type :`chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys`. 
  8. now you can restart your ssh connection using `sudo service ssh restart`. 
  9. you can now connect with `grader` using `ssh grader@18.215.131.65 -i ~/.ssh/key.rsa`.
  
  
## 5- changing the port from 22 to 2200 :-
  1. use `sudo nano /etc/ssh/sshd_config` to modify the `sshd_config` file.
  2. change the port 22 to 2200.
  3. then restart the ssh connection `sudo service ssh restart`.
  
  
## 6- the Firewall configuration (UFW) :-
  1. you can use `sudo ufw status` to be sure that the status for the firewall is inactive.
  2. deny all incomming using `sudo ufw default deny incoming`.
  3. allow all outgoing using `sudo ufw default allow outgoing`.
  4. allow the required ports only using :-
  
    sudo ufw allow 2200/tcp
    sudo ufw allow 80/tcp
    sudo ufw allow 123/udp
    
  5. now you can enable your firewall using `sudo ufw enable`.
  6. to check your status again use `sudo ufw status`.
  
  
## 7- timezone :-
  you have to be sure that your time zone is UTC using `sudo dpkg-reconfigure tzdata`
  
  
## 8- Install and configure Apache to serve a Python mod_wsgi application :-
  1. Install Apache using `sudo apt-get install apache2`.
  2. Install mod_wsgi using `sudo apt-get install python-setuptools libapache2-mod-wsgi`.
  3. then you can restart Apache using `sudo service apache2 restart`.
  
  
## 9- PostgreSQL :-
  1. install and configure PostgreSQL using :
  
    sudo apt-get install postgresql
    
  2. Login as user `postgres` using `sudo su - postgres`.
  3. now create new user and database both named catalog and set the password as "password" then give the user catalog permission to catalog app using the follow commands :-
  
    postgres=# CREATE DATABASE catalog;
    postgres=# CREATE USER catalog;
    postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
    postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
    
  4. to Quit postgreSQL use `postgres=# \q`
  5. to exit from `postgres` user use `exit`.
  
