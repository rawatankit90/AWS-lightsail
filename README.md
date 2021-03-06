# Deploying application Over the AWS Cloud

The goal of the project is to deployed the item catalog application developed earlier to be accessible over WEB. For this Amazon Cloud Platform
will be used, Apache Web Server and apache Plugin mod_wsgi [for python] will be used.

## About the Application
The application that is being deployed is name as Item Catalog which was developed as part of the udacity project only.  
The application is developed using following languages and Open source APIs.  
      *python 2.7 with flask framework,Postgresql, Google Oauth API, Foursquare API*   
To Get More info about application, check below Link  
            URL :[ Item_Catalog ](https://github.com/rawatankit90/item_catalog)  

# Accessing the Application
  Click on the link  to access the deployed application :  http://ec2-18-219-88-85.us-east-2.compute.amazonaws.com/

# Creating an AWS account and setup

1) Create an AWS account
2) Add Lightsail service from AWS
    * The service is free for first 30 days *
3) Select Linux Distribution  ->  'OS' only option -> ubuntu 16.04
4) Click on Create after selecting 5$free tier.
5) Wait for the M/C to show status 'running'
6) Attach the Static IP in the networking tab -> note down the **IP address** as it will be helpful in future
7) Go to the Amazon Account and download the default Private Key
8) Open the gitbash in your local computer
9) Create a folder in your computer using `$ mkdir .ssh`
10) Place the downloaded key in the folder just created above.ssh
11) Give permission chmod 600 to the key file
12) Perform ssh to lightsail accounts using below command.
      `ssh -i LightsailDefault.pem ubuntu@18.219.88.85`


# Create a New User account 'grader' and give sudo access

1) ssh from local computer to lightsail account as mentioned in last step of above section
2) Add a new user called grader:  
      `$ sudo adduser grader `
3) Create a new file under the suoders directory:  
      `$ sudo nano /etc/sudoers.d/grader`
3.1) Paste the below code in the above newly created file   
      `grader ALL=(ALL:ALL) ALL`, then save it.

4) Install Finger to verify the user  
     `$ sudo apt-get install finger`
5) verify the user is created  
     `$ finger`

# Update all currently installed packages
  Run Below 2 command to update the packages
  1) `$ sudo apt-get update`  -> It updates the source list
  2) `$ sudo apt-get upgrade` -> It updates the packages

# Configure the local timezone to UTC
  1) Open time configuration dialog and set it to UTC with: `$ sudo dpkg-reconfigure tzdata`
  2) Install ntp daemon ntpd for a better synchronization of the server's time over the network connection
      `$ sudo apt-get install ntp`

# Configure the key-based authentication for grader user
  1) Login into grader account from the ubuntu account using sudo su grader
      `$ sudo su grader`
    'Kind of a inception movie feeling :)' (home to ubuntu and from ubuntu to grader)
  2) In the home directory create a hidden directory name .ssh using below commands
      `$ mkdir .ssh`
  3) Change directory
      `$ cd .ssh/`
  4) Generate a public-private key using below, and give a filename to store the file
      `$ ssh-keygen`
  5) Copy the contents of file after opening
      `$ cat grader`
  6) Create a new file grader in the .ssh directory present in your local system and paste the content in it.
  7) Create a new file with name authorized_keys
      `$ touch authorized_keys`
  8) Copy the contents of the public key generated in step 4 '.pub' in the authorized_keys file
  9) Connect ssh directly into grader accounts from local putty or gitbash
      `$ ssh -i grader.pem grader@18.219.88.85`

# Disable ssh login for root user

  1) Find the PermitRootLogin line and edit it to no.  
      `$ sudo vim /etc/ssh/sshd_config`

# Enforce key-based authentication
  1) Find the PasswordAuthentication line and edit it to no.
  {default: no}
      `$ sudo vim /etc/ssh/sshd_config`

# Configure SSH to Port 2200 and disable Root login
  1) Find the Port line and edit it to 2200
      `$ sudo vim /etc/ssh/sshd_config`

## Restart SSH Service:
      `$ service sshd restart`

# Configure the lightsail Firewall to open port 2200
  Go to the instance management page on the Networking tab under Firewall present in Amazon account.  
  open port 2200 by selecting custom option.

# Configure the Uncomplicated Firewall (UFW)
  `$  sudo ufw default deny incoming`
  `$  sudo ufw allow SSH`
  `$  sudo ufw default allow outgoing`
  `$  sudo ufw allow www`
  `$  sudo ufw allow 2222/tcp`
  `$  sudo ufw allow ntp`

## Enable the Firewall
    `$ sudo ufw enable`
## Check the status of Firewall
    `$ sudo ufw status`

# Validate the SSH Keybased login on new Port number
  `$ ssh -i grader.pem grader@18.219.88.85 -p2200`


# Install Apache, mod_wsgi

1) Install Apache web server:
    `$ sudo apt-get install apache2`
2) Open a browser and open your public ip address, e.g. http://18.219.88.85/ - It should Open a Ubuntu page which says   
    'It works!' on the top of the page.
3) Install mod_wsgi for serving Python apps from Apache
    `$ sudo apt-get install libapache2-mod-wsgi python-dev`
4) Enable mod_wsgi: `$ sudo a2enmod wsgi`
5) Restart the Apache server for mod_wsgi to load:
    `$ sudo service apache2 restart`

# Create Folder Structure for application deployment
1) Move to the www directory:
    `$ cd /var/www`
2) Setup a directory for the app, item_catalog:
    `$ sudo mkdir item_catalog`
3) Move inside this directory using the following command and create another directory with same name
    `$ cd item_catalog` and `$ sudo mkdir item_catalog`
4) Then, move inside this directory and create two subdirectories named static and templates using the following commands:
    `$ cd item_catalog` and `$ sudo mkdir static templates`

## Install PIP, Flask, Virtual environment and other project dependencies

Setting up a virtual environment will keep the application and its dependencies isolated from the main system.  
Changes to it will not affect the cloud server's system configurations.In this step, we will create a  
virtual environment for our flask application.  

We will use pip to install virtualenv and Flask. If pip is not installed, install it on Ubuntu through apt-get.

  1)` $ sudo apt-get install python-pip`  
  2)` $ sudo pip install virtualenv  `  
  3) Set virtual environment to name 'venv':
    `    $ sudo virtualenv venv`  
  4) Change permissions to the virtual environment folder:
`      $ sudo chmod -R 777 venv`  
  5) Activate the virtual environment:
`      $ source venv/bin/activate `  
  6) Install Flask inside the virtual environment:
    `  $ pip install Flask`   
  7) Install all the other project's dependencies:
    `  $ pip install bleach httplib2 request oauth2client sqlalchemy requests passlib`  
  7.1) Install all the other project's dependencies:
  `    $ sudo pip install python-psycopg2`  

  8) Deactivate the environment:
    ` $ deactivate`

# Install Git

    `  $sudo apt-get install git`
        * Git is already installed *
Configure your username:
    `    $ git config --global user.name <username>`
Configure your email:
    `  $ git config --global user.email <email>`

# Clone GitHub repository and make it web inaccessible
  1) `$ cd /var/www`
  2) Change owner for the catalog folder: `$ sudo chown -R grader:grader item_catalog.
  3) Clone the Github repository in the item_catalog folder present at the location cd/var/www/item_catalog/item_catalog
      `$ clone the catalog repository from Github: $ git clone https://github.com/rawatankit90/item_catalog.git item_catalog.
  4) Make a catalog.wsgi file to serve the application over the mod_wsgi.

  a)`  $ cd /var/www/item_catalog` and `$ sudo vim item_catalog.wsgi`
  b)  Paste in the following lines of code. Pay a careful attention while pasting as no white space should
  be present and code should follow python code guidelines:
    ```
      #!/usr/bin/python
      import sys
      import logging
      logging.basicConfig(stream=sys.stderr)
      sys.path.insert(0,"/var/www/item_catalog/")

      from item_catalog import app as application
      application.secret_key = 'whoamitocreateyou'
    ```

  5) Change the directory to item_catalog where the code is copied
      `$ cd item_catalog`

  6) Make a backup of project.py
  7) Rename the Project.py to __init__.py
    `$ mv project.py __init__.py`
  8) Modify the __init__.py using vi to change the client_secrets.json to actual path '/var/www/item_catalog/item_catalog/client_secrets.json'

      `$ vim __init__.py`

# Configure and enable a new virtual host

  1) Create a virtual host conifg file:
      `$ sudo nano /etc/apache2/sites-available/catalog.conf`
  2) Paste in the following lines of code:

  Get the server alias from the :http://www.hcidata.info/host2ip.cgi by entering the Static IP
```
<VirtualHost *:80>
                ServerName 18.219.88.85
                ServerAdmin admin@18.219.88.85
                ServerAlias ec2-18-219-88-85.us-east-2.compute.amazonaws.com
                WSGIScriptAlias / /var/www/item_catalog/item_catalog.wsgi
                WSGIDaemonProcess item_catalog python-path=/var/www/catalog:/var/www/item_catalog/item_catalog/venv/lib/python2.7/site-packages
                WSGIProcessGroup item_catalog
                <Directory /var/www/item_catalog/item_catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/item_catalog/item_catalog/static
                <Directory /var/www/item_catalog/item_catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

**The WSGIDaemonProcess line explicitly saying to use the virtual environment and its packages to run the application.**

3) Enable the virtual host:
    `$ sudo a2ensite catalog`
4) Restart Apache:
  `$ sudo service apache2 restart`


# Install and configure PostgreSQL
1) Install PostgreSQL:
  `  $ sudo apt-get install postgresql postgresql-contrib`
2) Postgres is automatically creating a new user during its installation, whose name is 'postgres'.
  That is a tusted user who can access the database software. So let's change the user with: $ sudo su - postgres, then connect to the database system with $ psql.
  Create a new user called 'catalog' with his password:  
    `# CREATE USER catalog WITH PASSWORD 'catalog';`
3) Give catalog user the CREATEDB capability:
    `# ALTER USER catalog CREATEDB;`
4)Create the 'catalog' database owned by catalog user:
    `# CREATE DATABASE catalog WITH OWNER catalog;`
5) Connect to the database:
    `# \c catalog`
6) Revoke all rights:
  `# REVOKE ALL ON SCHEMA public FROM public;`
7) Lock down the permissions to only let catalog role create tables:
  `# GRANT ALL ON SCHEMA public TO catalog;`
8) Log out from PostgreSQL:
  ` \q`
9) Then return to the grader user:
  `$ exit`
10) Inside the Flask application, the database connection is now performed with:
      `engine = create_engine('postgresql://catalog:catalog@localhost/catalog')`
11) Modify the model.py to change the engine to postgresql
      `engine = create_engine('postgresql://catalog:catalog@localhost/catalog')`
12) Modify the model.py to change make the email_id column unique for ForeignKey issue encountered
13) Modify the model.py to increase the Password Hash field length to 400 in user table
14) Run the following command to create the database and activate virtualenv
          ```
          $ cd /var/www/item_catalog/item_catalog
          $ source venv/bin/activate
          (venv)  $ python model.py
          ```
15) Run the following command to populate the database
  `(venv)  $ python populate_db.py`

16) To prevent potential attacks from the outer world we double check that no remote connections to the database are allowed. Open the following file:
          `$ sudo nano /etc/postgresql/9.5/main/pg_hba.conf` and edit it, if necessary


# Update OAuth authorized JavaScript origins and  Authorized redirect URI

To let users correctly log-in using google account, Modify the Javascript origin and Authorized URI   
  JavaScript origins :http://ec2-18-219-88-85.us-east-2.compute.amazonaws.com/   
  Authorized URI :http://ec2-18-219-88-85.us-east-2.compute.amazonaws.com/oauth2callback


Sources : [ Udacity ](https://classroom.udacity.com)
          [ Digitalocean ](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)
          @iliketomatoes
