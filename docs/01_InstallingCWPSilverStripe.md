# Installing CWP SilverStripe

To install the CWP version of SilverStripe its best to check the CWP page for instructions on how to do this. This page can be found here: https://www.cwp.govt.nz/developer-docs/en/1.8/getting_started/

Though the docs there seem a little out of date as the repositories have moved from Gitlab to Github, so follow the instructions below.

# Prerequisites

As detailed on the page there are some prerequisites such as having PHP, SQL database (such as mysql), and a web server like Apache2 to run the site. These should all be available on the machines in the training room.

## //++ @TODO check what is on the training machines and what would need to be installed.

* Apache?
* MySQL?
* PHP 5.6?
* Atom?
* MySQL workbench (or other DB browser)?
* /var/www exists?
* Users can alter /etc/apache2/sites-enabled?
* Also /hosts file?
* Also ensure I have an example of the config files needed so SilverStripe will run, as it can be a little tricky at times.

# Install process

* Change into your web server's document root
```
cd /var/www
```
* Create new project using Composer by running the following command
```
composer create-project cwp/cwp-installer museum
```
* Note: "museum" is the name of the site we are going to create in the command above, for your own sites in future use whatever name you desire for you project.
* Choose Yes if it asks you about removing the repository history (it may not)
* Change in to the directory: cd /museum
* Create an _ss_environment.php file (touch _ss_environment.php on the command line will do this)
* Add the following to the file (via Atom or your favourite editor)...
 * again "museum" is the name of the project in this case, for your other sites use the project name you have selected for that.

```php
<?php

/* What kind of environment is this: development, test, or live (ie, production)? */
define('SS_ENVIRONMENT_TYPE', 'dev');

/* Database connection */
define('SS_DATABASE_SERVER', 'localhost');
define('SS_DATABASE_USERNAME', 'root');
define('SS_DATABASE_PASSWORD', 'password');
define('SS_DATABASE_NAME', 'museum');

define('SS_DEFAULT_ADMIN_USERNAME', 'admin');
define('SS_DEFAULT_ADMIN_PASSWORD', 'password');

global $_FILE_TO_URL_MAPPING;
$_FILE_TO_URL_MAPPING[realpath($_SERVER['DOCUMENT_ROOT'])] = 'http://museum.local';
$_FILE_TO_URL_MAPPING['/var/www/museum'] = 'http://museum.local';

```

* Next create a configuration file in the apache/sites-enabled for this site
* cd /etc/apache2/sites-enabled
* create a file with the name of your website, i.e. museum.conf
 * Must have the .conf extension
* Then open the file

```
sudo touch museum.conf
sudo vim museum.conf
```

* Add the following to this file...

```
<VirtualHost *:80>
        # The ServerName directive
        ServerName museum.local

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/museum

        <Directory //var/www/museum/>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
        </Directory>

        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

* Save this file and exit.
 * Press Esc
 * Then type :wq and press enter
* Now edit your hosts file

```
sudo vim /etc/hosts
```

* Add this: 127.0.1.1  museum.local
* Save and exit
 * Press Esc
 * Type :wq and press enter
* Finally re-start apache

```
sudo service apache2 restart
```

* Now you can visit the site in your browser: http://museum.local

# Assets directory

* In your browser add /dev/build to the url of your website
* This causes the DB tables to be created (or any changes applied), it also creates some static files
* Scroll down and you may see some errors, these will be because the assets directory does not exist or does not have the correct permissions
* In the directory of your site in the terminal, do an ls to see all the directories to check if an assets directory exists
 * Create if needed with: mkdir assets
* The owner-group and permissions of the assets directory need to be a certain way so run these commands...
 * The username on the training computers is "train", other computers your username will be different.

```
sudo chown train:www-data assets
sudo chmod 775 assets
```

* Run /dev/build again from your browser, this time there should be no errors about an assets directory.

# Ensure mod rewrite is enabled

The mod rewrite needs to be enabled for Apache, please run this command in your terminal..

```
sudo a2enmod rewrite
```

# Ready to go

You should now be ready to go. Check by logging in to the admin section of your site by replacing /dev/build with /admin in the url, i.e. http://museum.local/admin.
The user name should be "admin", with the password "password" - this is what was defined in the _ss_environment.php file.

# Notes and Tips

* On a real server, available to others, such as for your QA and live sites, you would not normally have a default login in the _ss_environment.php file, and especially not admin/password. So ensure those 2 lines are not present in the file on your live site.
* _ss_enviroment.php should not be checked in to source control, you would create this file on each server/environment where your website is deployed as the DB connection information will likely be different with a specific user and password for the database of the site (again not root/password either).
* For additional security you can move the ss_environment.php up one level, out of the root directory of your site. SilverStripe is smart enough to find it there.

# Your own repository

You need somewhere to save the work done on the site created throughout the day. I recommend you use Github for this as pushing to a repository on Github means you have an off-pc backup of the site you can refer to in future.

Some of you will be familiar with Github, others not so much, but its pretty easy to use. Please create an account on http://github.com if you don't already have one and then create a repository to push code changes to throughout the day.

* Create repository on Github, perhaps the name is training-museum
* Git init in the root of the site on the command line

```
git init
```

* Add a remote to the github repository you created. The link needed below can be found by clicking the Green "clone or download" button and copying the HTTPS link...

```
git remote add origin <repo link>
```

* Add all files

```
git add .
```

* Do an initial commit

```
git commit -m "Initial Commit"
```

* Do the first push

```
git push
```

You should now have the base for the site we will create commited in to the github repo you created. If you go to the repository on Github in your browser you should now see a number of files.