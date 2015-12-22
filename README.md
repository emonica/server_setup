Linux Server Configuration

## Abstract
Configured an Apache server, a Postgresql server, in order to serve a toy store app.
All in a virtual environment on an AWS instance.

## Quick Setup
```
ssh -i ~/.ssh/udacity_key.rsa grader@52.35.180.109 -p 2200
http://ec2-52-35-180-109.us-west-2.compute.amazonaws.com
```

## The app
The app itself is a Flask app for a toy stores catalog.
User can see all the stores registered so far, for each store its corresponding 
toys, and for each toy its details. Authenticated users can create new entries 
and update and delete their own entries. 

Each toy can have an associated image. This can be a link to a web location,
supplied in a text field, or a local photo, that is uploaded via an upload 
button and then copied in the static folder.

Authentication is available for FB and Google users. After successful 
authentication, the user's photo will be displayed in the menu.


## Installed software
The following packages were installed system wide:
```
sudo apt-get install python-pip apache2 libapache2-mod-wsgi
sudo pip install virtualenv
sudo apt-get install postgresql postgresql-contrib
sudo apt-get build-dep python-psycopg2
sudo apt-get install git
```

Then, inside the newly created app specific virtual environment, 
the folowing packages were installed:
```
sudo pip install Flask
sudo pip install sqlalchemy
sudo pip install psycopg2
sudo pip install oauth2client
sudo pip install flask-seasurf
sudo pip install dicttoxml
```

## Configuration changes
1. Changed /etc/ssh/sshd_config for:
   - new ssh port 2200
   - deny root login (PermitRootLogin no)

1. Force grader's password to expire every 90 days. Even though there
is [research that proves password expiration is not neccessarily safer][1]

1. Configure the ufw firewall to:
   - allow http
   - allow ntp
   - limit 2200/tcp

1. Set time zone to UTC (the default)

1. Created ubuntu_autoupdate weekly cron job, that does the following:
```
apt-get update 
apt-get upgrade -y 
apt-get autoclean
```

1. Restrict access to .git files from web app, by having file /var/www/.htaccess:
```
RedirectMatch 404 /\.git
```

1. Created 2 users for psql: 
   - admin (owner of the toystoresdb database)
   - catalog (only has basic data access)

1. Granted catalog the proper permissions in order to access the database:
   - connect to db
   - select, insert, update, delete on all tables
   - usage, select on all sequences

1. Ran database_setup.py and popukate_database.py, to initialize the database, after
applying the following changes:
   - use "activate_this" in order to use the virtual env when running the scripts
   - modify the sqlalchemy code that initializes the DB engine in order to use postgres instead of SQLite

1. Added the client_secrets.json and fb_client_secrets.json files to the app root
in order to allow OAuth2 authentication to the web app through google and facebook.


## Resources
[1]: http://www.cs.unc.edu/~fabian/papers/PasswordExpire.pdf
[2]: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
[3]: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04
[4]: https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2
[5]: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
[6]: http://dba.stackexchange.com/questions/33943/granting-access-to-all-tables-for-a-user

