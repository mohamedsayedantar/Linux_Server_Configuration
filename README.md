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
    6. to log into your machine type `ssh ubuntu@34.205.27.161 -i ~/.ssh/key.rsa`, as my ip=`34.205.27.161` and my file name is `key.rsa`.
    
    
## 3- new user named grader :-
    1. to create a new user type `sudo adduser grader`, add the password for grader, fill the information if you want.
    2. type `sudo nano /etc/sudoers.d/grader` to add file named grader to make grader able to use sudo command and add `grader ALL=(ALL:ALL) ALL`.
    3. then change to grader user using `su - grader` and type your password.
    4. add the `.ssh` file using `mkdir .ssh`.
    5. using `touch .ssh/authorized_keys` to add the authorized_keys file.
    6. now copy the key inside key.rsa file and type `nano .ssh/authorized_keys` to add this key to the authorized_keys file.
    7. type :`chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys`.
    8. now you can restart your ssh connection using `sudo service ssh restart`.
    
    
