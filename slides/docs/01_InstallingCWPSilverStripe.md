# Installing SilverStripe


## Installing SilverStripe
SilverStripe is installed with PHP's composer package manager. At a minimum, all you need is this:

<small>`composer require silverstripe/framework ^4`</small>

This creates a "headless" framework for basic model-view-controller applications such as REST APIs.


## Installing SilverStripe
A headless SilverStripe application is not very useful on its own. Most projects will use the CMS recipe:
<small>`composer require silverstripe/recipe-cms ^4`</small>

This creates a base project with the CMS, framework, and some commonly used addons for basic page creation.


## Installing SilverStripe
A "recipe" is a common paradigm for installing and managing larger SilverStripe projects. Government agencies, e-commerce platforms, and commercial websites will use recipes to manage their work.


## Installing SilverStripe (CWP)
The Common Web Platform is a collection of the CMS, framework, and common modules used to support government agency functions and operations throughout New Zealand. 


## Installing SilverStripe (CWP)
To install the CWP recipe it's best to check the CWP page for instructions on how to do this. This page can be found here: <a href="https://www.cwp.govt.nz/developer-docs/en/2/getting_started/" target="\_blank">https://www.cwp.govt.nz/developer-docs/en/2/getting_started/</a>. We will follow these instructions later.


## Prerequisites
Installation and configuration are the biggest barriers to starting SilverStripe development, so I will teach these topics today.

There are some prerequisites such as having PHP, a SQL database (such as mysql), and a web server like Apache2 to run the site. None of these are present on the training room computers so we need to install them first.


## Prerequisites (continued)
Many users will find it preferable to use Docker or WAMP/MAMP/LAMP stacks to handle these steps for them. Configuring these environments can be tricky: teaching the basics here is often still required for those too.


### You need to install PHP, Apache, and MySQL

This is an excellent guide: 
<small>https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-ubuntu-18-04</small>

Production environments will often differ, but this approach is suitable for development on most environments.


#### Install Apache

```
    sudo apt-get update
    sudo apt-get install apache2
```


#### Apache:: mod_rewrite

SilverStripe requires mod_rewrite.  Activate it within Apache:

```
sudo a2enmod rewrite
sudo service apache2 restart
```



#### PHP

Our training environment will use PHP 7.2. Alternatively, you can develop on a Vagrant or docker instance.

Install PHP and SilverStripe's dependencies

<small class="w-100">
```
sudo apt install php php-curl php-gd php-mbstring
sudo apt install php-tidy php-intl
sudo apt install php-mysql php-xml
sudo apt install php-dom php-zip unzip libapache2-mod-php
sudo apt install php-xdebug php-cli
```
</small>



#### Composer

Installing a recipe is almost always done with composer. It is the easiest way to install the project from scratch and start developing. Many deployment tools will deploy in this way.


#### Composer (continued)
Tools such as FabricDeploy or PHP Deployer make use of composer to build the project first then upload the result to a server. Some will checkout the code from git _on the server_ and run `composer install`.


#### Let's install Composer

Download instructions are here: https://getcomposer.org/download/

Run this script: 

<small>`curl https://getcomposer.org/installer | php`</small>

Then install it locally:
<small>`sudo mv composer.phar /usr/local/bin/composer`</small>

And test it:

<small>`composer about`</small>


#### Git

Composer and the CWP service both depend on `git clone` to work. It should be installed on Ubuntu already, but if it is not then run this command:

<small>`sudo apt-get install git`</small>



#### MySQL

* MySQL can (and should) be installed on a different instance for performance reasons. There's no harm in running the webserver and the DB on the same machine.

* Many services use MariaDB instead, which is a more freely licensed version of MySQL. There are very few observable differences, and MySQL is fine for development.


#### MySQL 

Install with this command: 

<small>`sudo apt-get install mysql-server`</small>

<small>`mysql_secure_installation`</small>


#### MySQL :: Set a root password
Don't forget your root password!  Write it down, you will need it later.


#### MySQL :: Set a root password
* [ ] Optionally use VALIDATE PASSWORD PLUGIN if you intend to set up other users.
* [N] Don't change root password, because we just set it.
* [Y] Remove anonymous users
* [Y] Disallow root remotely, this is a development machine.
* [Y] Remove test database and access.
* [Y] Reload privilege tables


### Configuring your virtualhost

On Ubuntu, we will add a new virtualhost to /etc/apache2/sites-available/mysite.localhost.conf


### Configuring your virtualhost

<small class="w-100">
```
<VirtualHost *:80>
	ServerName mysite.localhost
    
    #if working with subsites, uncomment and add subdomains here
	#ServerAlias subdomain.mysite.localhost

	ServerAdmin elliotsawyer@catalyst.net.nz
    #note the "public" folder is the docroot.
    #We will install the CWP project in this location. 
    #Don't worry if it doesn't exist yet
    #Your docroot and directory may not match mine. 
	DocumentRoot /home/ubuntu/mysite/public

	<Directory /home/ubuntu/mysite/public>
		Options -Indexes +FollowSymLinks +MultiViews
		AllowOverride All
		Require all granted
	</Directory>

	
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
</small>


### Configuring your virtualhost

Enable your site. Ignore any warnings about the docroot:

<small class="w-100">
```
sudo a2ensite mysite.localhost
sudo service apache2 restart
```
</small>

### /etc/hosts

```
#add this to the end of the file:
#this should match your ServerName
127.0.0.1   mysite.localhost
```


### Update Apache

* Change priority of index.php to be left-most in `/etc/apache2/mods-enabled/dir.conf`

```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```


### Update Apache

* Restart apache:  _sudo service apache2 restart_



#### Other tips:

Change your Apache User and Group to run as your local user (i.e. ubuntu). 
<small class="w-100">
```
sudo nano /etc/apache2/envvars

#Replace `export APACHE_RUN_USER=www-data` with `export APACHE_RUN_USER=ubuntu`
#Replace `export APACHE_RUN_GROUP=www-data` with `export APACHE_RUN_GROUP=ubuntu`

`sudo service apache2 restart`
```
</small>
Don't do this in a production environment! The webserver user should have minimal privileges


### Test your setup

Visit http://mysite.localhost/x.php after running this command: 
<small class="w-100">
```
mkdir -p ~/mysite/public
echo "<?=phpinfo()?>" >> ~/mysite/public/x.php. You should see some details about your PHP installation
```
</small>

Delete this directory before proceding with your SilverStripe installation
<small class="w-100">
```
rm -rf mysite
```
</small>


## Create your SilverStripe project

We will now install CWP. Installing the CWP project can be done with the following command:
```
cd ~
composer create-project cwp/cwp-installer mysite ^2
```

This will take a while to run (~10 minutes).


### What's happening here?
```
composer create-project cwp/cwp-installer mysite ^2
```

* We're using **composer** to install CWP

* **create-project** pulls a Github repository commonly known as a "recipe"

* **cwp/cwp-installer** is the name of this recipe

* **^2** means "install the most recent stable version of major release 2"



### Folder structure
* SilverStripe is modular - every piece of code you write is considered to be a module, including the contents of your `app` folder. 
```
mysite
    app
    public
    themes
        yourtheme
        watea
        starter
    vendor
    .env
```


### app
* The "app" folder contains all of your PHP code and project-specific templates. 
* You'll define any custom pagetypes, models, dataextensions, YML config, and other project-specific stuff in here. 
* **Never** modify modules in the `vendor` folder directly, unless you're pushing changes back to a version controlled project.


#### public
* The "public" folder contains all publicly visible assets. 
* This includes documents and images uploaded into the CMS, as well as symlinks to the JS, CSS, imagery, and other assets that are whitelisted by your theme. 
* You will rarely need to edit anything in this folder. Most public changes can be made in your themes instead


#### more about public
* SilverStripe serves all requests to the web from the /public folder. 
* All other parts of your codebase are protected from the webserver, which does not require direct public access to them. 
* This is also where the CMS `assets` folder places files uploaded from the CMS.
* This entire folder and its subdirectories should be readable by the webserver user. `assets` is writeable.


#### themes
* The "themes" folder is where your front-end theme (or multiple themes) will live. 
* Your theme will "expose" JS, CSS, and other files to the public folder.
* Your site can use a single theme, or it can use multiple themes which "cascade" down to a base theme. 


#### themes
* You'll specify a theme in app/_config/theme.yml. In CWP there's currently one loaded by default:

```
---
Name: cwptheme
---
SilverStripe\View\SSViewer:
  themes:
    - '$public'
    - 'starter'
    - '$default'
```


#### themes
* **$public** and **$default** are special cases of the theme loader that need to be included for historical reasons. 
* Everything in the **themes** folder is symlinked to **public**, which contains public resources such as CSS, JS, and other web assets like fonts.
* **$public** serves as a special type of theme that will take precedence over all others.
* **$default** is a fallback theme that all **framework** controllers use to render a skeleton layout


#### vendor
* The CWP installer will set up your entire SilverStripe project for you.
* All required projects are installed to the **vendor** folder and (usually) never need to be modified by the developer. 


### .env file

* If you have used previous versions of SilverStripe, you may be familiar with the _ss_environment.php file included with dev projects. 

* This has been replaced with **.env** text file for SilverStripe 4.


### .env file
* Create a `.env` with the following content:
```
SS_ENVIRONMENT_TYPE="dev"
SS_DATABASE_SERVER="localhost"
SS_DATABASE_NAME="mysite"
SS_DATABASE_USERNAME="root"
SS_DATABASE_PASSWORD="YourRootPassword"
SS_DEFAULT_ADMIN_USERNAME="administrator"
SS_DEFAULT_ADMIN_PASSWORD="TestTest!!!!!!!!"
```


### .env file
* SS_DEFAULT_ADMIN_USERNAME and SS_DEFAULT_ADMIN_PASSWORD create a default administrator user, which you can use to access the CMS for the first time. 

* A default account is considered a security risk. It should be disabled once you have created an account with a valid email address.

* Don't forget to set **SS_ENVIRONMENT_TYPE="dev"** when you first install. It defaults to "live", which supresses all frontend website errors until changed.


## Building your database
* Run the following command from your `mysite` folder:

```
vendor/bin/sake dev/build flush=
```

* You can also visit `http://mysite.localhost/dev/build?flush=` in a web browser. This may require an administrator login.

* Remember this command, you'll need it a lot!


## Conclusion
* At this point, you should have CWP installed with a very basic theme, which is known as the "starter" theme. 
* You can use this theme as a basis for your own custom frontend development, or you can install a new theme and build on top of it. 
* We'll cover this more in a future slide


## Saving your work

The CWP project includes a .gitignore file for files that don't need to be tracked, as well as some sensible defaults for .editorconfig and test suites. 

Now would be a prudent time to create an "initial commit" to your git repository and push it to the remote repository for safekeeping. Make a note of the clone URL for your git repository, it might look like this: 

    git@github.com:you/installing-silverstripe.git
    https://github.com/you/installing-silverstripe.git


## Saving your work

<small class="w-100">
```
git remote origin add https://github.com/you/installing-silverstripe.git
git add .
git commit -nM 'Initial commit'
git push -u origin master
```
</small>


## Notes and Tips

* On a real server such as for your QA and live sites, you would not normally have a default login in the .env file, and especially not admin/pass. So ensure those 2 lines are not present in the file on your live site.


## Notes and Tips
* .env should not be checked in to source control, you would create this file on each server/environment where your website is deployed as the DB connection information will likely be different with a specific user and password for the database of the site (again not root/password either).


## Notes and Tips
* .env should only be used when the site is in `dev` mode. In a production environment, these should be set as system environment variables instead. You can do this with SetEnv in your apache virtualhost. You could also add them to `/etc/apache2/envvars`


## Further reading

* CWP getting started <a href="https://www.cwp.govt.nz/developer-docs/en/2/getting_started/" target="\_blank">https://www.cwp.govt.nz/developer-docs/en/2/getting_started/</a>


## What's next?
* In our next topic, we will show you how to install an already-existing CWP theme, so you don't have to build one for scratch (don't worry, we'll do that too!).

* [Lesson 02 - More on Project Structure](02_SiteProjectStructure.md)