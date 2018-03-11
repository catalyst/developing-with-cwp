# Installing CWP SilverStripe

To install the CWP version of SilverStripe its best to check the CWP page for instructions on how to do this. This page can be found here: https://www.cwp.govt.nz/developer-docs/en/1.8/getting_started/

Though the docs there seem a little out of date as the repositories have moved from Gitlab to Github, so follow the instructions below to set up SilverStripe on your training room computer.

# Prerequisites

As detailed on the page there are some prerequisites such as having PHP, SQL database (such as mysql), and a web server like Apache2 to run the site. None of these are present on the training room computers so need to be installed.

## Apache

```
sudo apt-get install apache2 -y
```

Apache should start automatically after its installed.

### Ensure mod rewrite is enabled

The mod rewrite needs to be enabled for Apache, please run this command in your terminal..

```
sudo a2enmod rewrite
sudo service apache2 restart
```

And we also need to alter a couple of settings in the apache2 config so the re-write actually works for the SilverStripe site

```
sudo vim /etc/apache2/apache2.conf
```

Scroll down until you find the section of the file which contains security information for <Directory /var/www/> and update it to look like the below. We are allowing override.

```
<Directory /var/www/>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order allow,deny
        Require all granted
</Directory>
```

In Vim press i to enter edit mode, type your changes, then press Esc followed by :wq to write the changes and then quit vim.

#### Quick Vim cheat sheet

* i = enter edit mode
* Esc = exit edit mode
* :w = write changes
* :q = quit
* :wq = write then quit

## MySQL

```
sudo apt-get install mysql-server -y
```

When it asks you to set a password for the root user, please use "password" for the password since these training room PCs are wiped after every training session. On a real set up, you should enter a strong password for the root mysql user.

Also its always good practice to secure the mysql installation, so please run this command. Choose N when it asks if you would like to change the root password, otherwise enter Y(es) or press enter for the remaining questions.

```
sudo /usr/bin/mysql_secure_installation
```

## PHP 5.6

Please run these commands to install PHP 5.6 and the PHP modules SilverStripe needs...

```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install php7.0 php5.6 php5.6-mysql php-gettext php5.6-mbstring php-mbstring php7.0-mbstring php-xdebug libapache2-mod-php5.6 libapache2-mod-php7.0 php5.6-curl php5.6-xml php5.6-mcrypt
```

Note that a WARNING may output in the terminal, its OK to ignore this.

### Downgrade PHP version from PHP 7 to PHP 5.6

At this time its best to run SilverStripe 3.x in PHP 5.6, it will not install and run on the latest PHP 7. To do this now please run these commands...

```
sudo a2dismod php7.0 ; sudo a2enmod php5.6 ; sudo service apache2 restart
sudo update-alternatives --set php /usr/bin/php5.6
```

If desired you can run the following commands to confirm the installation (it will print out the directory were these are installed).

For the php -v command this will print out the current version of PHP. Please ensure it says 5.6 and not 7.x

```
which apache2
which mysql
which php
php -v
```

## Install Composer

Composer is needed to install SilverStripe. So please install composer by running these commands. If you get any RED coloured errors in the terminal please STOP at this point and let your instructor know. Its best to resolve any issues with the composer install at this time, otherwise you may need to repeat some of the steps below.

```
sudo apt-get install curl php-cli php-mbstring git unzip
cd ~
curl -sS https://getcomposer.org/installer -o composer-setup.php
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

Reference: https://poweruphosting.com/blog/install-composer-ubuntu/

To check if the installation was successful, type "composer" in your terminal and press enter. Composer should output a list of options.

# SilverStripe Install process

* Change into your web server's document root
```
cd /var
```
* Change the permissions of the www folder inside /var to be owned by 'train' which is the user you are logged in to the training room PCs as. This will eliminate any permission issues while trying to work inside the /var/www folder.
```
sudo chown train:www-data www -R
```
* Change in to the www directory
```
cd www
```
* Create new project using Composer by running the following command
```
composer create-project cwp/cwp-installer museum
```
* Note: "museum" is the name of the site we are going to create in the command above, for your own sites in future use whatever name you desire for you project.
* Choose Yes if it asks you about removing the repository history (it may not)
* Change in to the directory: cd museum
* Create an \_ss_environment.php file (touch \_ss_environment.php on the command line will do this)
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

## Assets directory

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

## Ready to go

You should now be ready to go. Check by logging in to the admin section of your site by replacing /dev/build with /admin in the url, i.e. http://museum.local/admin.
The user name should be "admin", with the password "password" - this is what was defined in the \_ss_environment.php file.

## Notes and Tips

* On a real server, available to others, such as for your QA and live sites, you would not normally have a default login in the \_ss_environment.php file, and especially not admin/password. So ensure those 2 lines are not present in the file on your live site.
* \_ss_enviroment.php should not be checked in to source control, you would create this file on each server/environment where your website is deployed as the DB connection information will likely be different with a specific user and password for the database of the site (again not root/password either).
* For additional security you can move the ss_environment.php up one level, out of the root directory of your site. SilverStripe is smart enough to find it there.

# GIT repository for the site we will build

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

# Additional tools

I recommend installing the additional things on your training room computer to assist with development.

## Atom SilverStripe package

Finally, we will be using the Atom editor for this course. In order for SilverStripe files to be syntax highlighted correctly, please install this package for Atom like so...

* Edit -> Preferences
* Choose + Install in the Preferences Dialog menu (upper-left)
* Type "SilverStripe" in the search for packages
* Install the atom-silverstripe package

## MySQL Workbench

In Ubuntu's Application Manager, search for MYSQL and install the "MySQL Workbench" application. This will allow you to easily see the database structure of SilverStripe sites, and perform lots of other functions such as querying data, running SQL statements etc.

You can create a connection to the mysql server on your local machine in MySQL workbench by clicking the little (+) icon to the right of the MySQL Connections title.

All you should need to do is give it a name, by default the parameters are to localhost. Click "Test Connection" then OK. It will create a card under the MySQL connections which you can then click to connect to the local database. The password you enter is for the root user, this is the same password you entered when setting up MySQL - it should be just "password".

# Further reading/references

* CWP getting started https://www.cwp.govt.nz/developer-docs/en/1.8/getting_started/
* Interactive Vim tutorial http://www.openvim.com/
* Downgrading PHP on Ubuntu https://askubuntu.com/questions/761713/how-can-i-downgrade-from-php-7-to-php-5-6-on-ubuntu-16-04
* Atom: install packages https://flight-manual.atom.io/using-atom/sections/atom-packages/
* MySQL workbench https://www.mysql.com/products/workbench/

# Next

[Lesson 02 - Project Structure](02_SiteProjectStructure.md)
