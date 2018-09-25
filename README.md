# Linux Server Configuration
  this project takes a baseline installation of a Linux server and prepare it to host a web application.
  by securing the server from a number of attack vectors, installing and configuring the database server,
  and deploying the web applications onto it.
  
  visit the website at [http://18.215.131.65.xip.io](http://18.215.131.65.xip.io)
  while the static IP is `18.215.131.65` and the private IP is `172.26.12.149`.
  
  Note :
  
    Due to the recent change with Google OAuth,the amazon lightsail addresses or IPs are not valid origins for clients, so I have used xip.io, which is a service that provides a domain name for any IP address, to bypass this rule.
  
### installed software :
  1. on ubuntu :
      - Apache2
      - mod_wsgi
      - PostgreSQL
      - pip
  2. on pip :
     - Flask
     - http2lib
     - requests
     - sqlalchemy
     - Psycopg2
    

# several steps to finish the Server Configuration process :-


## 1- the server :-
  by using Ubuntu Linux server instance on Amazon Lightsail, follow these steps:- 
  1. creat an acount on `https://aws.amazon.com` if you don not have one. 
  2. log into `https://lightsail.aws.amazon.com/ls/webapp/home/instances` . 
  3. click on `create instance` . 
  4. choose `os only`, `ubuntu` . 
  5. choose your instance plan, then give your instance a hostname. 
  6. click create, and wait for the instance to start up. 
  for more information `https://serverpilot.io/docs/how-to-create-a-server-on-amazon-lightsail`. 
    
    
## 2- SSH Connection :-
  you can click on `Connect using SSH` and use the online terminal,
  or follow these steps to use your own terminal:- 
  1. go down and click on `Account page`, then click on `SSH Keys`. 
  2. go down and click on `Download` your Default Private Key. 
  3. move the file you downloaded to `~/.ssh/` using `mv` command. 
  4. rename this file to be `key.rsa`, or any name but with `.rsa` not `.pem`. 
  5. perform this command `chmod 600 ~/.ssh/lightsail_key.rsa` in your terminal. 
  6. to log into your machine type `ssh ubuntu@18.215.131.65 -i ~/.ssh/key.rsa`, as my ip=`18.215.131.65` and my file name is `key.rsa`. 
  
  
## 3- update :-
  update your software and packages using:-
  
    sudo apt-get update
    sudo apt-get upgrade
    
  to be sure that you are up to date use :
  
    sudo apt-get dist-upgrade
    sudo shutdown -r now
  and then log in again you have to see somthing like this :
  
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.4.0-1067-aws x86_64)

    * Documentation:  https://help.ubuntu.com
    * Management:     https://landscape.canonical.com
    * Support:        https://ubuntu.com/advantage

    Get cloud support with Ubuntu Advantage Cloud Guest:
        http://www.ubuntu.com/business/services/cloud

    0 packages can be updated.
    0 updates are security updates.

    New release '18.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.


    Last login: Tue Sep 25 10:09:51 2018 from 187.35.185.234
    
  be sure that `0 packages can be updated` and `0 updates are security updates`.
    
## 4- new user named grader :-
  1. to create a new user type `sudo adduser grader`, add the password for grader, fill the information if you want. 
  2. type `sudo nano /etc/sudoers.d/grader` to add file named grader to make grader able to use sudo command and add `grader ALL=(ALL:ALL) ALL`. 
  3. then change to grader user using `su - grader` and type your password. 
  4. add the `.ssh` file using `mkdir .ssh`. 
  5. using `touch .ssh/authorized_keys` to add the authorized_keys file. 
  6. now copy the key inside `.ssh/authorized_keys` file for ubuntu user and type `nano .ssh/authorized_keys` to add this key to the authorized_keys file for grader user. 
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
  

## 10- Catalog App project :-
  1. first you need to install git to clone your project :
    
    sudo apt-get install git
  2. you have to move to /var/www directory using `cd /var/www`.
  3. add FlaskApp directory using `sudo mkdir FlaskApp` then move to it `cd FlaskApp`.
  4. now clone you project to this directory using :
    
    sudo git clone https://github.com/mohamedsayedantar/Item_Catalog.git 
    
  5. rename the Item_Catalog to FlaskApp using `sudo mv ./Item_Catalog ./FlaskApp`
  6. change your directory to the new FlaskApp using `cd FlaskApp`
  7. now yor path must be `/var/www/FlaskApp/FlaskApp`.
  8. change the `project.py` file name to `__init__.py`.
  9. change `engine = create_engine('sqlite:///categories.db')` in `database_create.py`, `lotsofcategories.py` and `__init__.py`with :
    
    engine = create_engine('postgresql://catalog:password@localhost/catalog')
  
## 11- install pip and psycopg2 :-
  1. install python using `sudo apt-get install python-pip`.
  2. Install psycopg2 usong `sudo apt-get -qqy install postgresql python-psycopg2`.
  
  3. now you can create database and add the categories and items to it using :
  
    sudo python database_create.py
    sudo python lotsofcategories.py
    
    
## 12- Install the virtual environment :-
go to your app using `cd /var/www/FlaskApp/FlaskApp`, then :
  1. Install the virtual environment using `sudo apt-get install python-virtualenv`.
  2. create the new virtual environment using `sudo virtualenv venv`.
  3. activate the virutal environment `source venv/bin/activate`.
    
    
## 12- .wsgi file :-
  1.create flaskapp.wsgi file at `/var/www/FlaskApp` using :
    
    cd /var/www/FlaskApp
    sudo nano flaskapp.wsgi
    
  2. add the following to this file :
  
    #!/usr/bin/python
    import site
    site.addsitedir('/var/www/FlsakApp/FlaskApp/venv/lib/pythonX.X/site-packages')
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/FlaskApp/")

    from FlaskApp import app as application
    application.secret_key = 'Add your secret key'


## 13- configure the Virtual Host :-
  1. first you have to create FlaskApp.conf using :
    
    sudo nano /etc/apache2/sites-available/FlaskApp.conf
    
  2. then add these lines to this file :
    
    <VirtualHost *:80>
        ServerName  18.215.131.65.xip.io
        ServerAdmin mohamedsayed99.ms@gmail.com
        ServerAlias 18.215.131.65.xip.io
        WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
        <Directory /var/www/FlaskApp/FlaskApp/>
                Order allow,deny
                Allow from all
        </Directory>
        Alias /static /var/www/FlaskApp/FlaskApp/static
        <Directory /var/www/FlaskApp/FlaskApp/static/>
                Order allow,deny
                Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    
  3. now you can enable your virtual host using `sudo a2ensite FlaskApp`.
  4. for restarting your Apache use `sudo service apache2 restart`.
  
  
## 14- Authenticate login through Google :-
  1. go to [Google Cloud Plateform](https://console.cloud.google.com)
  2. click on `Go to APIs overview` then choose `Credentials` then choose your project.
  3. add `http://18.215.131.65.xip.io` to `Authorized JavaScript origins`.
  4. and add these 3 URIs to `Authorized redirect URIs` :
    
    http://18.215.131.65.xip.io/
    http://18.215.131.65.xip.io/login
    http://18.215.131.65.xip.io/gconnect
  5. edit the json file in the FlaskApp using `sudo nano client_secrets.json` and add these URIs to it.
    
  Note: you will need to add your domain to the authorized domains first. 
  
  
## 15- install dependencies :-
  perform these lines to install the requiered dependencies:
  
    pip install httplib2
    pip install requests
    pip install --upgrade oauth2client
    pip install sqlalchemy
    pip install flask
    sudo apt-get install libpq-dev
    pip install psycopg2


## 16- __init__.py edits :-
  1. change the line `CLIENT_ID = json.loads(open('client_secrets.json', 'r').read())['web']['client_id']` with this line `json.loads(open(r'/var/www/FlaskApp/FlaskApp/client_secrets.json', 'r').read())['web']['client_id']`.
  2. change the line 69 `oauth_flow = flow_from_clientsecrets('client_secrets.json', scope='')` with              `oauth_flow = flow_from_clientsecrets('/var/www/FlaskApp/FlaskApp/client_secrets.json', scope='')`.
  3. change `app.run(host='0.0.0.0', port=5000)` with `app.run(host="0.0.0.0")`.
  
  4. now you can use `sudo service apache2 restart` to restart Apache and visit the website at `http://18.215.131.65.xip.io`
  
  
## sources :-
  - `http://flask.pocoo.org/docs/0.12/installation/`
  - `https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps`
  - `https://stackoverflow.com/questions/49779283/permission-denied-to-generate-login-hint-for-target-domain-when-hosted-on-aws`
