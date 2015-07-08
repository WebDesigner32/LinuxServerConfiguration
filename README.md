##**Landon's Restaurant Menu App Linux Server Configuration**##

To view and use my completed project, please visit: `http://52.27.40.156/`.

Ignoring Google+ due to my personal login information, log in with fb using
the following test user info to use the app:

Email: `developer32@hotmail.com`, Password: `5H@!u?k9s#`

As can been seen from the above URL, the IP address is `52.27.40.156`.
From `~ $`, access the server by typing in the following:

`ssh -v grader@52.27.40.156 -p2200`, and then click `Enter`.

Once this is complete, you must provide the password, which is: `bell24OK`.
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

1. Download Private Key

2. Record your Public IP Address

3. Move the private key file into the folder ~/.ssh. If you downloaded the file
to the Downloads folder, just execute the following command in your terminal:

`mv ~/Downloads/udacity_key.rsa ~/.ssh/`

4. Type in:

`chmod 600 ~/.ssh/udacity_key.rsa`

5. Type in:

`ssh -i ~/.ssh/udacity_key.rsa root@PUBLICIPADDRESSGOESHERE`

**3. Create a new user named `grader`**

1. To make a user, type in:

`adduser grader`

**4. Give the grader the permission to sudo**

1. Type in:

`visudo`

2. Under `root ALL=(ALL:ALL) ALL`, type in:

`grader ALL=(ALL:ALL) ALL`

3. To save changes and exit, type in: `CTRL+X`, then `Shift+Y`, followed by
`Enter`. Use this to save changes and exit all added features of this project!

**5. Update all currently installed packages**

1. Switch to the new user `grader` by typing in:

`su - grader`.

2. Type in:

`sudo apt-get update`,

and continue with the update process.

3. Type in:

`sudo apt-get upgrade`,

and continue with the upgrade process.

**6. Change the SSH port from 22 to 2200 (includes SSH)**

1. In the root, type in:

`nano /etc/ssh/sshd_config`

2. Replace the current `Port` with `2200`.

3. Replace `without-password` with `no` for `PermitRootLogin`.

4. Replace `no` with `yes` for `Password Authentication`.

5. At the bottom of the file, type in:

`UseDNS no`

6. Just after `UseDNS no`, type in:

`AllowUsers grader`

7. Save changes and exit.

8. Type in:

 `/etc/init.d/ssh restart` or `restart ssh`

9. Switch to user grader by typing in:

`su - grader`

10. Type in:

`ssh - keygen`

11. Type in:

`cat ~/.ssh/id_rsa.pub | ssh -p 2200 grader@REMOTEHOSTGOESHERE
“mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys”`

12. To sign in as grader when closing out of the Terminal, type in:

`ssh -v grader@PUBLICIPADDRESSGOESHERE -p2200`

13. In grader, type in:

`sudo nano /etc/ssh/sshd_config`

14. Replace `yes` with `no` for `Password Authentication`.

15. Save changes and exit.

**7. Configure the Uncomplicated Firewall (UFW) to only allow incoming
connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)**

1. Type in:

`sudo ufw enable`

2. Type in:

`sudo ufw allow 2200/tcp`

3. Type in:

`sudo ufw allow 80/tcp`

4. Type in:

`sudo ufw allow 123/udp`

**8. Configure the local timezone to UTC**

1. Type in:

`sudo dpkg-reconfigure tzdata`

2. From the provided list, select `OK` for `None of the above`.

3. From the provided list, select `OK` for `UTC`.

**9. Install and configure Apache to serve a Python mod_wsgi application**

1. Type in:

`sudo apt-get install apache2`

2. Go to `http://PUBLICIPADDRESSGOESHERE/ in your browser. You will see a
message that states "It works!" on the page.

3. Type in:

`sudo apt-get install python-setuptools libapache2-mod-wsgi`

4. Type in:

`sudo service apache2 restart`

**11. Install git, clone and setup your Catalog App project:**

1. Type in:

`sudo apt-get install git`

2. Type in:

`git config --global user.name "YOURNAMEGOESHERE"`

3. Type in:

`git config --global user.email "YOUREMAILADDRESSGOESHERE"`

4. Type in:

sudo apt-get install libapache2-mod-wsgi python-dev

5. You should have mod_wsgi enabled, but if it is not, type in:

`sudo a2enmod wsgi`

6. Type in:

`cd /var/www`

7. Type in:

`sudo mkdir catalog`

8. Type in:

`cd catalog`

9. Type in:

`sudo mkdir catalog`

10. Type in:

`cd catalog`

11. Type in:

`sudo mkdir static templates`

12. Type in:

`sudo nano __init__.py`

13. Paste in:

 `from flask import Flask`
 `app = Flask(__name__)`
 `@app.route("/")`
 `def hello():`
     `return "Hello, I love Digital Ocean!"
 `if __name__ == "__main__":`
     `app.run()`

14. Type in:

`sudo apt-get install python-pip`

15. Type in:

`sudo pip install virtualenv`

16. Type in:

`sudo virtualenv venv`

17. Type in:

`sudo chmod -R 777 venv`,

to enable all permissions.

18. Type in:

`source venv/bin/activate`

19. Type in:

`pip install Flask`

20. Type in:

`python __init__.py`

21. Type in `deactivate` to deactivate the virtual environment.

22. Type in:

`sudo nano /etc/apache2/sites-available/catalog.conf`

23. Inside that file, paste in:

`<VirtualHost *:80>`
                `ServerName PUBLICIPADDRESSGOESHERE`
                `ServerAdmin admin@PUBLICIPADDRESSGOESHERE`
                `WSGIScriptAlias / /var/www/catalog/catalog.wsgi`
                `<Directory /var/www/catalog/catalog/>`
                        `Order allow,deny`
                        `Allow from all`
                `</Directory>`
                `Alias /static /var/www/catalog/catalog/static`
                `<Directory /var/www/catalog/catalog/static/>`
                        `Order allow,deny`
                        `Allow from all`
                `</Directory>`
                `ErrorLog ${APACHE_LOG_DIR}/error.log`
                `LogLevel warn`
                `CustomLog ${APACHE_LOG_DIR}/access.log combined`
`</VirtualHost>`

24. Save changes and exit.

25. Type in:

`sudo a2ensite catalog`

26. Type in:

`cd /var/www/catalog`

27. Type in:

`sudo nano catalog.wsgi`

28. Paste in:

`#!/usr/bin/python`
`import sys`
`import logging`
`logging.basicConfig(stream=sys.stderr)`
`sys.path.insert(0,"/var/www/catalog/")`

`from catalog import app as application`
`application.secret_key = 'Add your secret key'`

29. Save changes and exit.

30. Type in:

`sudo service apache2 restart`

31. Type in:

`sudo git clone https://github.com/WebDesigner32/Catalog.git`

32. Move the newly acquired Catalog directory to `/var/www/catalog/catalog`.

33. Delete the extra empty directory.

34. Type in:

`cd /var/www/catalog/`

35. Type in:

`sudo nano .htaccess`

36. Paste in:

`RedirectMatch 404 /\.git`,

to make the .git directory not publicly accessible from the browser.

37. Save changes and exit.

38.Type in:

`source venv/bin/activate`

39. Type in:

`pip install httplib2`

40. Type in:

`pip install requests`

41. Type in:

`sudo pip install sqlalchemy`

42. Type in:

`sudo pip install --upgrade oauth2client`

43. Type in:

`sudo apt-get install python-psycopg2`

**10. Install and configure PostgreSQL:**

1. Type in:

`sudo apt-get install postgresql postgresql-contrib`

2. Type in:

`sudo nano /etc/postgresql/9.3/main/pg_hba.conf`,

to check that there are no remote connections allowed.

3. Type in:

`sudo nano database_setup.py`

4. Find the line of code beginning with "engine" and replace that line with:

`engine = create_engine(
'postgresql://catalog:PASSWORDFORDBGOESHERE@localhost/catalog')`

5. Save changes and exit.

6. Type in:

`sudo nano project.py`

7. Find the line of code beginning with "engine" and replace that line with:

`engine = create_engine(
'postgresql://catalog:PASSWORDFORDBGOESHERE@localhost/catalog')`

8. Anywhere you see fb_client_secrets.json or client_secrets.json, specify the
absolute path in project.py.

9. Save changes and exit.

10. Type in:

`sudo nano lotsofmenus.py`

11. Find the line of code beginning with "engine" and replace that line with:

`engine = create_engine(
'postgresql://catalog:PASSWORDFORDBGOESHERE@localhost/catalog')`

12. Save changes and exit.

13. Type in:

`mv project.py __init__.py`

14. Type in:

`sudo adduser catalog`

15. Provide a password.

16. Type in:

`sudo su - postgres`

17. Type in:

`psql`

18. Type in:

`CREATE USER catalog WITH PASSWORD 'PASSWORDFORDBGOESHERE';`

19. Type in:

`ALTER USER catalog CREATEDB;`

20. Type in:

`CREATE DATABASE catalog WITH OWNER catalog;`

21. Type in:

`\c catalog`

22. Type in:

`REVOKE ALL ON SCHEMA public FROM public;`

23. Type in:

`GRANT ALL ON SCHEMA public TO catalog;`

24. Type in:

`\q`

25. Type in:

`exit`

26. Type in:

`python database_setup.py`

*If you run into errors, you might need to make further installations specific
to the error(s) received.

27. Type in:

`python lotsofmenus.py`

**11. Install git, clone and setup your Catalog App project:**

1. Type in:

`sudo service apache2 restart`

2. Go to http://PUBLICIPADDRESSGOESHERE/ using your browser. The application
should be present!

3. If you receive any errors, run:

`sudo tail /var/log/apache2/error.log`.

Most errors should be quickly eliminated.

4. Go to http://www.hcidata.info/host2ip.cgi in your browser and obtain your
host name and your Public IP Address.

5. Find your project at the Google Developers Console:

https://console.developers.google.com/project

6. Go to `APIs & auth`, then `Credentials`, followed by `Edit settings`.

7. Provide the host name and Public IP Address for JavaScript origins.

8. Provide the host name and oauth2callback to Redirect URIs. For instance:
http://ec2-52-27-40-156.us-west-2.compute.amazonaws.com/oauth2callback

9. Update the changes.

10. In your browser, go to https://developers.facebook.com/apps/.

11. Click on the application.

12. Go to `Settings`.

13. Provide your Public IP Address in `Site URL` like so:
http://PUBLICIPADDRESSGOESHERE/

14. Provide your Email Address in `Contact Email`.

15. Click `Save Changes`.

16. Click on the sidebar, `Status & Review`.

17. Select `YES` for `Do you want to make this app and all its live features
available to the general public?`

##**Sources**##

1. https://www.udacity.com/account#!/development_environment
2. https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps
3. http://askubuntu.com/questions/94102/what-is-the-difference-between-apt-get-update-and-upgrade
4. http://askubuntu.com/questions/16650/create-a-new-ssh-user-on-ubuntu-server
5. https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server
6. https://help.ubuntu.com/community/UFW
7. http://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt
8. http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html
9. http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none
10. http://askubuntu.com/questions/256013/could-not-reliably-determine-the-servers-fully-qualified-domain-name
11. https://help.github.com/articles/set-up-git/#platform-linux
12. https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
13. http://flask.pocoo.org/docs/0.10/quickstart/
14. http://stackoverflow.com/questions/2643612/activate-a-python-virtual-environment-using-activate-this-py-in-a-fabfile-on-win
15. http://stackoverflow.com/questions/20276895/could-not-create-work-tree-dir-example-com-permission-denied
16. http://unix.stackexchange.com/questions/50487/how-to-move-all-files-and-folders-via-mv-command
17. http://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible
18.  https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
19. http://serverfault.com/questions/110154/whats-the-default-superuser-username-password-for-postgres-after-a-new-install
20. http://blog.trackets.com/2013/08/19/postgresql-basics-by-example.html
21. http://superuser.com/questions/769749/creating-user-with-password-or-changing-password-doesnt-work-in-postgresql
22. http://stackoverflow.com/questions/10572498/importerror-no-module-named-sqlalchemy
23. http://stackoverflow.com/questions/12906351/importerror-no-module-named-psycopg2
24. http://stackoverflow.com/questions/12201928/python-open-method-ioerror-errno-2-no-such-file-or-directory
25. http://www.hcidata.info/host2ip.cgi
26. https://developers.google.com/identity/protocols/OAuth2
27. Other articles read for knowledge boost.
