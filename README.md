# Linux-Server-Configuration[Book Catalog]
Installation of a Linux distribution on a virtual machine and prepare it to host your web application(Item Catalog). It includes installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

The EC2 URL is : http://ec2-13-233-94-109.ap-south-1.compute.amazonaws.com/

Local IP address: http://13.233.94.109/

SSH port- 2200

Login with: ssh -i ~/.ssh/snehal.pem -p 2200 grader@13.233.94.109


# Launch a new Ubuntu Linux server instance on Amazon Lightsail

* Login into [Amazon Lightsail](https://signin.aws.amazon.com/) using an Amazon Web Services account.

* Once you are login into the site, click Create instance.

* Choose Linux/Unix platform, OS Only and Ubuntu 16.04 LTS.

* Choose a instance plan (I took the cheapest, $5/month).

* Keep the default name provided by AWS or rename your instance.

* Click the Create button to create the instance.

* Wait for the instance to start up.

* Development Environment Information Details:-
1. Public IP Address - 13.233.94.109

2. Private Key - Can't be shared

**Resources:** [How to Create Instance](https://medium.com/@mariasurmenok/creating-a-server-with-amazon-lightsail-11c377cf814c)


# Follow the instructions provided to SSH into your server

1. Move the private key file into the folder ~/.ssh (where ~ is your environment's home directory).
                       
   `$ sudo mv ~/Downloads/snehal.pem ~/.ssh/`

2. Change the key permission so that only owner can read and write
                      
   `$ chmod 600 ~/.ssh/snehal.pem`

3. SSH into the instance
                     
   `ssh -i ~/.ssh/snehal.pem root@13.233.94.109`

# Create New User

1. Create a new user named grader
      
   `sudo adduser grader`

2. To check the User(grader) information :
   * ` sudo apt-get install finger`
   *  `finger grader`
   It is give you additional information(login , name , shell, directory, phone number etc) of User-grader

3. Give the grader the permission to sudo

   `sudo visudo`

(edit the sudoers file . it is save to use sudo visudo to edit the sudoers file otherwise file will not be saved)
add the below line of code after root ALL=(ALL:ALL) ALL grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter)
Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)

4. You can check the grader entry by below command: 
      
   `sudo cat /etc/sudoers`

# Update all currently installed packages

1. `sudo apt-get update` - command will update list of packages and their versions on your machine.

2. `sudo apt-get upgrade` - command will install the packages


# Change the SSH port from 22 to 2200
1. Login as root and open the ssdh_config file using below command:
   * `root@ip-13.233.94.109:~# nano /etc/ssh/sshd_config`
* change port from 22 to 2200
* change PermitRootLogin without-password to PermitRootLogin no . it is disable root login.
* change PasswordAuthentication from no to yes.
* add AllowUsers grader at end of the file so that we will login through grader.

2. restart the SSH service : `sudo service ssh restart`

# Set ssh login using keys
1. generate keys on local machine using  `ssh-keygen`

2. Save keygen file into (/home/user/.ssh/project5).and fill the password . 2 keys will be generated, public key (project5.pub) and identification key(project5).

3. Login into grader account using 

   `ssh -v grader@"public_IP_address" -p 2200` 

Type the password that you have fill during user creation (sudo adduser grader step 3) 
snehal@sw-snehal:~$ ssh -v grader@13.233.94.109 -p 2200 grader@ip-13.233.94.109 password :
if the password is correct , you will login as grader account: grader@ip-13.233.94.109:~$

4. make a directory in grader account :
 
   `sudo mkdir .ssh`

5. make a authorized_keys file using 
        
   `touch .ssh/authorized_keys`

6. from your local machine,copy the contents of public key(Project5.pub).
        
   `cat .ssh/project5.pub`

7. paste that contents on authorized_keys of grader account using

   `nano authorized_keys` and save it .

8. give the permissions : 
        
   `chmod 700 .ssh` and 
   
   `chmod 644 .ssh/authorized_keys`

9. do nano `/etc/ssh/sshd_config `, change PasswordAuthentication to no .

10. sudo service ssh restart.

11. `ssh grader@13.233.94.109 -p 2200 -i ~/.ssh/project5` in new terminal .A pop-up window will open for authentication. just fill the password that you have fill during ssh-keygen creation.

# Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

1. check the firewall status using 
           `sudo ufw status`
2. block all incoming connections on all ports using 
            `sudo ufw default deny incoming`
3. allow outgoing connections on all ports using
            `sudo ufw default allow outgoing`
4. allow incoming connection for SSH(port 2200) using
            `sudo ufw allow 2200/tcp`
5. allow incoming connection for HTTP(port 80) using 
            `sudo ufw allow 80/tcp`
6. allow incoming connection for NTP(port 123) using 
            `sudo ufw allow 123/udp`
7. check the added rules using 
    `        sudo ufw show added`
8. enable the firewall using
            `sudo ufw enable`
9. check whether firewall is enable or not using 
            `sudo ufw status`

# Configure the local timezone to UTC

1. configure timezone using `sudo dpkg-reconfigure tzdata` ( select none of the above and then set timezone to UTC)
Resources - timezone to UTC

2. It is already set to UTC.

# Configure cron scripts to automatically manage package updates

1. Install unattended-upgrades if not already installed using command:
    `
   `$ sudo apt-get install unattended-upgrades`

2. Enable it using command:
 
   `$ sudo dpkg-reconfigure --priority=low unattended-upgrades`

# Install and configure Apache to serve a Python mod_wsgi application:
1. install apache using `sudo apt-get install apache2`

2. type 13.233.94.109 (public IP address) on URL . You will see the apache ubuntu default page .

3. Install mod_wsgi using `sudo apt-get install libapache2-mod-wsgi`

# Installing PostgreSQL Python dependencies:

* Installing PostgreSQL Python dependencies:
  
  `$ sudo apt-get install libpq-dev python-dev`

* Installing PostgreSQL:

  `$ sudo apt-get install postgresql postgresql-contrib`

* Check if no remote connections are allowed :
  
  `$ sudo cat /etc/postgresql/9.3/main/pg_hba.conf`

* Login as postgres User (Default User), and get into PostgreSQL shell:
  * $ sudo su - postgres
  * $ psql

* Create a new User named *catalogs*:  `# CREATE USER catalogs WITH PASSWORD 'password';`

* Create a new DB named *catalogs*: `# CREATE DATABASE catalogs WITH OWNER catalogs;`

* Connect to the database *catalogs* : `# \c catalogs`

* Revoke all rights: `# REVOKE ALL ON SCHEMA public FROM public;`

* Lock down the permissions only to user *catalogs*: `# GRANT ALL ON SCHEMA public TO catalogs;`

* Log out from PostgreSQL: `# \q`. Then return to the *grader* user: `$ exit`

* Inside the Flask application edit database_setup.py,lotsofitems.py and _init_.py:

engine = create_engine('postgresql://catalog:yourPassword@localhost/catalog')

# Install git, clone and setup your Catalog App project.
1. Install git using `sudo apt-get install git`

2. Use `cd /var/www` to move to the /var/www directory

3. Create the application directory 
            `sudo mkdir FlaskApp`

4. Move inside this directory using 
               `cd FlaskApp`

5. Clone the ItemCatalog App to the virtual machine git clone
    `https://github.com/9snehal/Bookcatalog.git`

6. Rename the project's name
  ` sudo mv ./Bookcatalog ./FlaskApp`


7. Move to the inner FlaskApp directory using 
               `cd FlaskApp`

8. Rename final.py to _init_.py using 
    `sudo mv final.py _init_.py`

# Create Virtalenvironment
1. we will create a virtual environment for our flask application
         
`sudo pip install virtualenv`

2. Set enviornment name using : `sudo virtualenv venv`

3. Install Flask in that environment by activating the virtual environment using : `source venv/bin/activate`

4. To deactivate the environment : `deactivate`

# Install Flask and other dependencies
    $ sudo apt-get install python-pip
    $ sudo pip install Flask
    $ sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils
    $ sudo pip install requests
    $ sudo apt-get -qqy install postgresql python-psycopg2

# Configure and Enable a New Virtual Host

1. Create FlaskApp.conf to edit: 

   `sudo nano /etc/apache2/sites-available/FlaskApp.conf`

2. Add the following lines of code to the file to configure the virtual host.

          <VirtualHost *:80>
	          ServerName 52.24.125.52
	          ServerAdmin qiaowei8993@gmail.com
	          WSGIScriptAlias / /var/www/FlaskApp/FlaskApp/flaskapp.wsgi
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

3. Enable the virtual host with the following command: 
                 
   `sudo a2ensite FlaskApp`

# Create the .wsgi File

1. Create the .wsgi File under /var/www/FlaskApp/FlaskApp:[where _init_.py file is present]

   ` $ cd /var/www/FlaskApp/FlaskApp`
        
   ` $ sudo nano flaskapp.wsgi` 

2. Add the following lines of code to the flaskapp.wsgi file:

        #!/usr/bin/python
        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0,"/var/www/FlaskApp/FlaskApp/")

        from _init_ import app as application
        application.secret_key = 'Add your secret key'

# Directory structure should be :
```
|--------FlaskApp
|----------------FlaskApp
|-----------------------static
|-----------------------templates
|-----------------------venv
|-----------------------__init__.py
|----------------flaskapp.wsgi
```

# Restart Apache
Restart Apache : `sudo service apache2 restart`


# Run the application
1. Go to the /var/www/catalog/catalog directory execute :
  
   `python database_setup.py`

   `python lotsofitems.py`
         
   `python __init__.py`

2. Restart Apache

* Restart Apache : `sudo service apache2 restart`

3. Type public IPaddress (http://13.233.94.109/) on URL and you will see your Itemcatalog project.

4. Note: It might be some errors occured when you execute _init_.py file.It can be :
related to client_secrets.json and fb_client_secrets.json files. You need to give absolute path to these files .change the 
CLIENT_ID = json.loads( open('client_secrets.json', 'r').read())['web']['client_id'] to
open(r'/var/www/FlaskApp/FlaskApp/client_secrets.json', 'r').read())['web']['client_id']
Similarly for `fb_client_secrets.json` file.

5. check your errors in /var/log/apache2/error.log files type:
    `sudo tail -10 /var/log/apache2/error.log to see last 10 lines of file`

6. Make sure after you recorrect your error , restart the apache2 server.

# Oauth Login
1. go to [hcidata](http://www.hcidata.info/host2ip.cgi) and get the host name of public IP address: 13.233.94.109 , Host Name: ec2-13-233-94-109.ap-south-1.compute.amazonaws.com


2. sudo nano /etc/apache2/sites-available/FlaskApp.conf and add the hostname below ServerAdmin: paste
   
   ` ServerAlias ec2-13-233-94-109.ap-south-1.compute.amazonaws.com`

3. enable the virtual host : `sudo a2ensite FlaskApp`

4. restart the apacheserver : `sudo service apache2 restart`

# Google Authorization steps:

1. Go to  [ console.developer](https://console.developers.google.com/)

2. click on Credentails --> edit

3. add you hostname (http://ec2-13-233-94-109.ap-south-1.compute.amazonaws.com) and public IP address (http://13.233.94.109) to Authorised JavaScript origins.

4. add hostname (http://ec2-13-233-94-109.ap-south-1.compute.amazonaws.com/oauth2callback) to Authorised redirect URIs.

5. update the client_secret.json file too(adding hostname and public IP address).

# Facebook Authorization steps:

* Go to[ developer.facebook](https://developers.facebook.com/)

* open your application and click on Facebook Login --> settings.

* Add hostname and public IP address to Valid OAuth redirect URIs and save it.
update the fb_client_secret.json file too.

# Restart Apache
Restart Apache : 

  `sudo service apache2 restart`

