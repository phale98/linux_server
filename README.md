# linux_server

# FSND Linux Configuration

### Getting Started
1. Log into or create an AWS account.
1. Choose OS only.
1. Select the Ubuntu Distribution.
1. Go with the five dollar plan.
1. Name your server.
1. Click the create button.
1. Log inot the server with ssh.
1. Update the packages with `sudo apt-get update`
1. Upgrade the packages with `sudo apt-get upgrade`
1. Configure the time zone with `sudo dpkg-reconfigure`, UTC is under none of the above.

### Configuring OAuth
1. Go to Google Cloud Platform.
1. Under API & Services click Credentials
1. Edit the authorized JavaScript origins to inlcude the server's public ip address and the domain name,
   which can be found using a service like hcidata.info.
1. In Authorized Redirects make sure to include the /gconnect or other google connect method.

### Creating the grader user with sudo rights
1. Log into your new server.
2. Run `sudo adduser grader`
3. Fill out the prompts for creating the user, name grader, password grader.
4. `sudo touch /etc/sudoers.d/grader`
5. edit the file with `sudo vim /etc/sudoers.d/grader`
6. Press escape, then i. Add the line `grader ALL=(ALL:ALL) ALL`
6. Save and quit with esc and :wq.

### Enable SSH authentication
#### Local Machine (Not Server)
1. Open your termnial (Windows will have to use something like git bash or Cygwin).
2. Navigate to the users home directory `cd ~`
3. Use `ls -a` to see if you have a .shh directory, you dont use `mkdir .ssh` to create one.
4. Run  `ssh-keygen`
5. Type .ssh/[NAME OF KEY HERE] the name is arbitrary.
6. Accept with enter.
6. Use `cd .ssh` to enter directory and copy the output of `cat [NAME_OF_KEY].pub`

#### On Amazon Server
1. Switch to grader using `sudo su grader`
2. Repeat steps 2 and 3 from the local machine section.
3. Set file permissions `chmod 700 .ssh` only the owner should have full permission.
4. Use `touch .ssh/authorized_keys` to create an authorized_key file.
5. Use `chmod 600 .ssh/authorized_keys` so onlyt he owner can read or write the file.
6. Paste the public key for yoru pair into the file using a text editor like nano or vim.

### Local Machine SSH Connection
You should be able to connect by using the command `grader@[SERVER_IP] -p 2200 -i ~/.ssh/[NAME_OF_KEY_ON_LOCAL_MACHINE]`

### Some Server Configurations For Our App
1. Install apache with `sudo apt-get install apache2`
2. Get mod_wsgi `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. Restart the apache service with `sudo service apache2 restart`

### Get PostgreSQL
1. Use `sudo apt-get install postgresql`
2. Login using `sudo su - postgres`
3. To use postgresql type `psql`
4. Create a new database the application with `postgres=# CREATE DATABASE catalog;`
   and `postgres=# CREATE USER catalog;`
5. Set the password for the user to password `postgres =# ALTER ROLE catalog WITH PASSWORD 'password';`
6. Give the catalog user access to catalog db `postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog to catalog;`
7. Quit and exit with `postgres=# \q` `exit`

### Clone your App.
1. Check if you have git `git -v` if you don't run `sudo apt-get install git`
2. Navigate to www directory with `cd /var/www`
3. Clone your app with `git clone [YOUR_PROJECT_URI]`
4. Remove files not related to your app, like Vagrant file, or forum.

### Configure the App
1. Rename your application file to `__init__.py` with `sudo mv [APP_NAME] __init__.py`
2. If you used SQLite like me reconfigure your app to use postgreSQL by changing every line that says
   `sqlite:///[DATABASE_NAME]` to `postgresql://catalog:password@localhost/catalog`
3. This will most likely need to be changed in `databse_setup.py` as well. 
4. Get the pip application `sudo apt-get install python-pip`
5. Get the virtual enviornment `sudo pip install virtualenv`
6. Create a new virtual enviornment `sudo virtualenv [NAME_OF_VENV]`
7. Start it `source [NAME_OF_VENV]/bin/activate`
8. Use pip to install requests, httplib2, oauth2client, and sqlalchemy. Make sure your virtual enviornment is on.
9. Create and edit the virtual host with `sudo vim /etc/apache2/sites-available/catalog.conf`
10. Make it look like this:
   `<VirtualHost *:80>
  ServerName [PublicIPAddress]
  ServerAdmin [Email]
  ServerAlias [Host Name]
  WSGIScriptAlias / /var/www/catalog/catalog.wsgi
  <Directory /var/www/catalog/catalog/>
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
</VirtualHost>`
11. Use `sudo a2ensite catalog` to start the virtual host.
12. Make the wsgi file you mentioned in the virtual host iwth `sudo nano /var/www/catalog/catalog.wsgi`
13. Make it match:
   `#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'Add your secret key'`
14. Restart apache with `sudo service apache2 restart`.

### Setup Firewall
1. Note: Be very carful here as it is possible to lock yourself out of your remote server.
2. Run `vim /etc/ssh/sshd_config`
3. Change port number listed from 22 to 2200.
4. Change the PermiteRootLogin option to no.
5. Change PasswordAuthentication to no.
6. Run the following commands to configure the firewall:
   `sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable`
7. In the Amazon Lightsail Instance, click on the 3 dots to manage your server. Select networking and add these rules:
   HTTP TCP 80, CUSTOM UDP 123, CUSTOM TCP 2200.

