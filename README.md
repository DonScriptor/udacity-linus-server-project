# udacity-linus-server-project
udacity nano degree linux server configuration


* Public IP address : 35.180.79.168
* Private IP address: 172.26.4.30

* SSH port : 2200

* installed software : apachy / flask / pip / sqlalchemy / PostgreSQL / mod-wsgi

* allowed ports in th ufw : 2200/ 80/ 123/ 

* to connect run in your terminal ( ssh udacity@35.180.79.168 -p 2200 -i ~/.ssh/udacity_key)

* grader password : 12345

* online resources : 
https://github.com/jaikathuria/FullStack-Project--8
https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04
https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-14-04-lts
https://serverfault.com/questions/262751/update-ubuntu-10-04/262773#262773
https://www.digitalocean.com/community/tutorials/how-to-tune-your-ssh-daemon-configuration-on-a-linux-vps
https://askubuntu.com/questions/14763/where-are-the-apache-and-php-log-files
https://askubuntu.com/questions/716610/sudo-aptitude-command-not-found/716611


*Configuration steps
 - downloaded default key provided by amazon lightsail and used to ssh to the server
 - created a new user [udacity]
 
	$ sudo adduser udacity
 
 - gave the udacity user superuser privileges
 
	$ sudo nano /etc/sudoers.d/udacity
	
 - added the following to its profile
	
	grader ALL=(ALL:ALL) ALL
	
 - created new SSH keys for udacity on my local machine and saved the private key locally
	
	- ssh-keygen
	
 - 	logged in temporarily as udacity to add the public ssh key to its
	
	$ su udacity
	$ cd /home/udacity/.ssh
	$ mkdir authorized-keys
 - Change the owner of the directory authorized-keys and its content to udacity

   $ sudo chown -R udacity:udacity /home/udacity/.ssh/ authorized-keys
	
 - logged in back as udacity by ssh to the server with the newly created ssh key pair
 - Update all currently installed packages
 
	$ sudo apt-get update
	$ sudo apt-get upgrade
	$ sudo apt-get aptitude
	$ sudo aptitude update
	$ sudo aptitude safe-upgrade
	
 - Enforce key-based authentication && Changing the SSH port from 22 to 2200 | Disable login for root user.
	
	$ sudo nano /etc/ssh/sshd_config
	- setting port 22 to 2200
	- setting PasswordAuthentication  to no
	- setting permittRootLogin to no

 - Configure the Uncomplicated Firewall (UFW)
 
	$ sudo ufw default deny incoming
	$ sudo ufw default allow outgoing
	$ sudo ufw allow 2200/tcp
	$ sudo ufw allow www
	$ sudo ufw allow ntp
	$ sudo ufw enable
	
 - Install and Configure dev dependencies:  Apache2 and mod-wsgi and Git
 
    $ sudo apt-get install apache2 libapache2-mod-wsgi git
    $ sudo a2enmod wsgi
	
 -  Install Flask and other dependencies
    $ sudo apt-get install python-pip
    $ sudo pip install Flask
    $ sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils	
	
 - Install and configure PostgreSQL and its python dependencies
	
	$ sudo apt-get install libpq-dev python-dev
	$ sudo apt-get install postgresql postgresql-contrib
	
 - disable remote connections
 
  $ sudo cat /etc/postgresql/9.3/main/pg_hba.conf
  
 - logging into postgesql and creating new user
	
	$ sudo su - postgres
	$ psql
	
	# CREATE USER catalog WITH PASSWORD 'password';
	# CREATE DATABASE catalog WITH OWNER catalog;
	# \c catalog
	# REVOKE ALL ON SCHEMA public FROM public;
	# GRANT ALL ON SCHEMA public TO catalog;
	
 - pointing the DB inside the flask app to the newly created catalog DB
	
	engine = create_engine('postgresql://catalog:123@localhost/catalog')
	
 
 - Make a catalog named directory in /var/www

    $ sudo mkdir /var/www/catalog
 
 - Change the owner of the directory catalog

   $ sudo chown -R grader:grader /var/www/catalog
 
 - Clone the item catalog to the catalog directory:

	$ git clone https://github.com/DonScriptor/udacity-server- catalog
	

 - Make a catalog.wsgi file to serve the application over the mod_wsgi. with content:

    $ touch catalog.wsgi && nano catalog.wsgi
	
 - added the following the catalog.wsgi file	
 
		import sys
		import logging
		logging.basicConfig(stream=sys.stderr)
		sys.path.insert(0, "/var/www/catalog/")

		from project import app as application
		
 - added the follo line to the default Virtual File
	
	$  sudo nano /etc/apache2/sites-available/000-default.conf
	
	  added: WSGIScriptAlias / /var/www/catalog/catalog.wsgi
  
 - Restart Apache to launch the app
 
 $ sudo service apache2 restart 
	
	
	
	
	
	
	
