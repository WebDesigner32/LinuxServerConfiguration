##**Landon's Restaurant Menu App Linux Server Configuration**##

To view and use my completed project, please visit: `http://52.27.40.156/`.

Ignoring Google+ due to my personal login information, log in with fb using
the following test user info to use the app:

Email: `developer32@hotmail.com`, Password: `5H@!u?k9s#`

As can been seen from the above URL, the IP address is `52.27.40.156`.
From `~ $`, access the server by typing in the following:

`ssh -v grader@52.27.40.156 -p2200`, and then click `Enter`.

Once this is complete, you must provide the password. You must request it from
me personally if I have not already provided it to you.
Once provided, click `Enter`. You are now logged in!

From there, you can access the same functionality from the project for my repo
`Catalog`.

##**Project Goals**##

This project takes a baseline installation of a Linux distribution on a virtual
machine and prepares it to host the web applications, to include installing
updates, securing it from a number of attack vectors and installing/configuring
web and database servers.

##**How to Run Application**##

**1. Launch your Virtual Machine.**

Start a new development environment.

**2. Follow the instructions provided to SSH into your server.**

Download Private Key

Record your Public IP Address

Move the private key file into the folder ~/.ssh. If you downloaded the file
to the Downloads folder, just execute the following command in your terminal:

`mv ~/Downloads/udacity_key.rsa ~/.ssh/`

Type in:

`chmod 600 ~/.ssh/udacity_key.rsa`

Type in:

`ssh -i ~/.ssh/udacity_key.rsa root@PUBLICIPADDRESSGOESHERE`

**3. Create a new user named `grader`**

To make a user, type in:

`adduser grader`

**4. Give `grader` the permission to sudo**

Type in:

`visudo`

Under `root ALL=(ALL:ALL) ALL`, type in:

`grader ALL=(ALL:ALL) ALL`

To save changes and exit, type in: `CTRL+X`, then `Shift+Y`, followed by
`Enter`. Use this to save changes and exit all added features of this project!

**5. Update all currently installed packages**

Switch to the new user `grader` by typing in:

`su - grader`

Type in:

`sudo apt-get update`

and continue with the update process.

Type in:

`sudo apt-get upgrade`

and continue with the upgrade process.

**6. Change the SSH port from 22 to 2200 (includes SSH)**

In the root, type in:

`nano /etc/ssh/sshd_config`

Replace the current `Port` with `2200`.

Replace `without-password` with `no` for `PermitRootLogin`.

Replace `no` with `yes` for `PasswordAuthentication`.

At the bottom of the file, type in:

`UseDNS no`

Just after `UseDNS no`, type in:

`AllowUsers grader`

Save changes and exit.

Type in:

 `/etc/init.d/ssh restart` or `restart ssh`

Switch to user `grader` by typing in:

`su - grader`

Type in:

`ssh - keygen`

Type in:

`cat ~/.ssh/id_rsa.pub | ssh -p 2200 grader@REMOTEHOSTGOESHERE
“mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys”`

To sign in as `grader` when closing out of the Terminal, type in:

`ssh -v grader@PUBLICIPADDRESSGOESHERE -p2200`

In `grader`, type in:

`sudo nano /etc/ssh/sshd_config`

Replace `yes` with `no` for `PasswordAuthentication`.

Save changes and exit.

**7. Configure the Uncomplicated Firewall (UFW) to only allow incoming
connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)**

Type in:

`sudo ufw enable`

Type in:

`sudo ufw allow 2200/tcp`

Type in:

`sudo ufw allow 80/tcp`

Type in:

`sudo ufw allow 123/udp`

**8. Configure the local timezone to UTC**

Type in:

`sudo dpkg-reconfigure tzdata`

From the provided list, select `OK` for `None of the above`.

From the provided list, select `OK` for `UTC`.

**9. Install and configure Apache to serve a Python mod_wsgi application**

Type in:

`sudo apt-get install apache2`

Go to `http://PUBLICIPADDRESSGOESHERE/` in your browser. You will see a
message that states "It works!" on the page.

Type in:

`sudo apt-get install python-setuptools libapache2-mod-wsgi`

Type in:

`sudo service apache2 restart`

**11. Install git, clone and setup your Catalog App project:**

Type in:

`sudo apt-get install git`

Type in:

`git config --global user.name "YOURNAMEGOESHERE"`

Type in:

`git config --global user.email "YOUREMAILADDRESSGOESHERE"`

Type in:

`sudo apt-get install libapache2-mod-wsgi python-dev`

You should have mod_wsgi enabled, but if it is not, type in:

`sudo a2enmod wsgi`

Type in:

`cd /var/www`

Type in:

`sudo mkdir catalog`

Type in:

`cd catalog`

Type in:

`sudo mkdir catalog`

Type in:

`cd catalog`

Type in:

`sudo mkdir static templates`

Type in:

`sudo nano __init__.py`

Paste in:
<pre><code>
 from flask import Flask
 app = Flask(__name__)
 @app.route("/")
 def hello():
     return "Hello, I love Digital Ocean!"
 if __name__ == "__main__":
     app.run()
</code></pre>
Type in:

`sudo apt-get install python-pip`

Type in:

`sudo pip install virtualenv`

Type in:

`sudo virtualenv venv`

Type in:

`sudo chmod -R 777 venv`

to enable all permissions.

Type in:

`source venv/bin/activate`

Type in:

`pip install Flask`

Type in:

`python __init__.py`

Type in `deactivate` to deactivate the virtual environment.

Type in:

`sudo nano /etc/apache2/sites-available/catalog.conf`

Inside that file, paste in:

<pre><code>
"
<VirtualHost *:80>
                ServerName PUBLICIPADDRESSGOESHERE
                ServerAdmin admin@PUBLICIPADDRESSGOESHERE
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
</VirtualHost>
"
</code></pre>
Save changes and exit.

Type in:

`sudo a2ensite catalog`

Type in:

`cd /var/www/catalog`

Type in:

`sudo nano catalog.wsgi`

Paste in:

<pre><code>
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'Add your secret key'
</code></pre>
Save changes and exit.

Type in:

`sudo service apache2 restart`

Type in:

`sudo git clone https://github.com/WebDesigner32/Catalog.git`

Move the newly acquired Catalog directory to `/var/www/catalog/catalog`.

Delete the extra empty directory.

Type in:

`cd /var/www/catalog/`

Type in:

`sudo nano .htaccess`

Paste in:

`RedirectMatch 404 /\.git`

to make the .git directory not publicly accessible from the browser.

Save changes and exit.

Type in:

`source venv/bin/activate`

Type in:

`pip install httplib2`

Type in:

`pip install requests`

Type in:

`sudo pip install sqlalchemy`

Type in:

`sudo pip install --upgrade oauth2client`

Type in:

`sudo apt-get install python-psycopg2`

**10. Install and configure PostgreSQL:**

Type in:

`sudo apt-get install postgresql postgresql-contrib`

Type in:

`sudo nano /etc/postgresql/9.3/main/pg_hba.conf`

to check that there are no remote connections allowed.

Type in:

`sudo nano database_setup.py`

Find the line of code beginning with "engine" and replace that line with:

`engine = create_engine(
'postgresql://catalog:PASSWORDFORDBGOESHERE@localhost/catalog')`

Save changes and exit.

Type in:

`sudo nano project.py`

Find the line of code beginning with "engine" and replace that line with:

`engine = create_engine(
'postgresql://catalog:PASSWORDFORDBGOESHERE@localhost/catalog')`

Anywhere you see `fb_client_secrets.json` or `client_secrets.json`, specify
the absolute path in project.py.

Save changes and exit.

Type in:

`sudo nano lotsofmenus.py`

Find the line of code beginning with "engine" and replace that line with:

`engine = create_engine(
'postgresql://catalog:PASSWORDFORDBGOESHERE@localhost/catalog')`

Save changes and exit.

Type in:

`mv project.py __init__.py`

Type in:

`sudo adduser catalog`

Provide a password.

Type in:

`sudo su - postgres`

Type in:

`psql`

Type in:

`CREATE USER catalog WITH PASSWORD 'PASSWORDFORDBGOESHERE';`

Type in:

`ALTER USER catalog CREATEDB;`

Type in:

`CREATE DATABASE catalog WITH OWNER catalog;`

Type in:

`\c catalog`

Type in:

`REVOKE ALL ON SCHEMA public FROM public;`

Type in:

`GRANT ALL ON SCHEMA public TO catalog;`

Type in:

`\q`

Type in:

`exit`

Type in:

`python database_setup.py`

If you run into errors, you might need to make further installations specific
to the error(s) received.

Type in:

`python lotsofmenus.py`

**11. Install git, clone and setup your Catalog App project:**

Type in:

`sudo service apache2 restart`

Go to `http://PUBLICIPADDRESSGOESHERE/` using your browser. The application
should be present!

If you receive any errors, run:

`sudo tail /var/log/apache2/error.log`

Most errors should be quickly eliminated.

Go to `http://www.hcidata.info/host2ip.cgi` in your browser and obtain your
host name and your Public IP Address.

Find your project at the Google Developers Console:

`https://console.developers.google.com/project`

Go to `APIs & auth`, then `Credentials`, followed by `Edit settings`.

Provide the host name and Public IP Address for JavaScript origins.

Provide the host name and oauth2callback to Redirect URIs. For instance:
`http://ec2-52-27-40-156.us-west-2.compute.amazonaws.com/oauth2callback`.

Update the changes.

In your browser, go to `https://developers.facebook.com/apps/`.

Click on the application.

Go to `Settings`.

Provide your Public IP Address in `Site URL` like so:
`http://PUBLICIPADDRESSGOESHERE/`

Provide your Email Address in `Contact Email`.

Click `Save Changes`.

Click on the sidebar, `Status & Review`.

Select `YES` for `Do you want to make this app and all its live features
available to the general public?`

##**Sources**##

1. `https://www.udacity.com/account#!/development_environment`
2. `https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps`
3. `http://askubuntu.com/questions/94102/what-is-the-difference-between-apt-get-update-and-upgrade`
4. `http://askubuntu.com/questions/16650/create-a-new-ssh-user-on-ubuntu-server`
5. `https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server`
6. `https://help.ubuntu.com/community/UFW`
7. `http://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt`
8. `http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html`
9. `http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none`
10. `http://askubuntu.com/questions/256013/could-not-reliably-determine-the-servers-fully-qualified-domain-name`
11. `https://help.github.com/articles/set-up-git/#platform-linux`
12. `https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps`
13. `http://flask.pocoo.org/docs/0.10/quickstart/`
14. `http://stackoverflow.com/questions/2643612/activate-a-python-virtual-environment-using-activate-this-py-in-a-fabfile-on-win`
15. `http://stackoverflow.com/questions/20276895/could-not-create-work-tree-dir-example-com-permission-denied`
16. `http://unix.stackexchange.com/questions/50487/how-to-move-all-files-and-folders-via-mv-command`
17. `http://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible`
18. `https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps`
19. `http://serverfault.com/questions/110154/whats-the-default-superuser-username-password-for-postgres-after-a-new-install`
20. `http://blog.trackets.com/2013/08/19/postgresql-basics-by-example.html`
21. `http://superuser.com/questions/769749/creating-user-with-password-or-changing-password-doesnt-work-in-postgresql`
22. `http://stackoverflow.com/questions/10572498/importerror-no-module-named-sqlalchemy`
23. `http://stackoverflow.com/questions/12906351/importerror-no-module-named-psycopg2`
24. `http://stackoverflow.com/questions/12201928/python-open-method-ioerror-errno-2-no-such-file-or-directory`
25. `http://www.hcidata.info/host2ip.cgi`
26. `https://developers.google.com/identity/protocols/OAuth2`
27. `https://developers.facebook.com/apps/`
28. `https://console.developers.google.com/project`
29. `http://52.27.40.156/`
30. Other articles and videos used to deepen understanding . . .
