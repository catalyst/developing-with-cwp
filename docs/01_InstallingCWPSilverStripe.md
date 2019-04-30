# Installing CWP SilverStripe

To install the CWP version of SilverStripe its best to check the CWP page for instructions on how to do this. This page can be found here: <a href="https://www.cwp.govt.nz/developer-docs/en/2/getting_started/" target="\_blank">https://www.cwp.govt.nz/developer-docs/en/2/getting_started/</a>

## Prerequisites

As detailed on the page there are some prerequisites such as having PHP, SQL database (such as mysql), and a web server like Apache2 to run the site. None of these are present on the training room computers so need to be installed.

### You need to install PHP, Apache, and MySQL

[This is an excellent guide](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04). Note that this is not the exact same environment as CWP, but it works very well for development.

#### Apache

On CWP, Nginx is used as a proxy for requests to Apache. This is not applicable in a dev environment but something to be aware of. We will not install nginx here.
```
    sudo apt-get update
    sudo apt-get install apache2
```

#### mod_rewrite

SilverStripe requires mod_rewrite.  Activate it with Apache:
```
sudo a2enmod rewrite
sudo service apache2 restart
```

#### PHP

Our training environment will PHP 7.0. Ideally you should install PHP 7.1 for CWP. Alternatively, you can develop on a Vagrant or docker instance.

* Install PHP and its dependencies
```
#core dependencies
sudo apt-get install php php-curl php-gd php-mbstring php-mcrypt php-tidy php-intl php-mysql php-xml php-dom php-zip unzip libapache2-mod-php
#developer tools
sudo apt-get install php-xdebug php-cli
```

#### Composer

Installing the CWP codebase is almost always done with composer. It is the easiest way to install the project from scratch and start developing. This is also how the CWP Dashboard deploys new versions of your website. 

Other tools, such as FabricDeploy or PHP Deployer, make use of composer to build the project first then upload the result to a server. 

[Download instructures here](https://getcomposer.org/download/) or run this script:
```
EXPECTED_SIGNATURE="$(wget -q -O - https://composer.github.io/installer.sig)"
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
ACTUAL_SIGNATURE="$(php -r "echo hash_file('sha384', 'composer-setup.php');")"

if [ "$EXPECTED_SIGNATURE" != "$ACTUAL_SIGNATURE" ]
then
    >&2 echo 'ERROR: Invalid installer signature'
    rm composer-setup.php
    exit 1
fi

php composer-setup.php --quiet
RESULT=$?
rm composer-setup.php
sudo mv composer.phar /usr/local/bin/composer
```

To check if the installation was successful, type "composer" in your terminal and press enter. Composer should output a list of options.

#### Git

Composer and the CWP service both depend on `git clone` to work. It should be installed on Ubuntu already, but if it is not then run this command:
```
sudo apt-get install git
```

#### MySQL

* MySQL can (and should) be installed on a different instance for performance reasons. There's no harm in running the webserver and the DB on the same machine.

The CWP service uses MariaDB, which is a more open version of MySQL. There are very few observable differences, and MySQL is fine for development.

```
sudo apt-get install mysql-server
mysql_secure_installation
```
Set a root password.
* [?] Optionally use `VALIDATE PASSWORD PLUGIN` if you intend to set up other users. 
* [N] Don't change root password, we just set it.
* [Y] Remove anonymous users
* [Y] Disallow root remotely, this is a development machine.
* [Y] Remove test database and access.
* [Y] Reload privilege tables

### Configuring your virtualhost

On Ubuntu, we will add a new virtualhost to /etc/apache2/sites-available/mysite.localhost.conf

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
		Options Indexes FollowSymLinks MultiViews
		AllowOverride All
		Require all granted
	</Directory>

	
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Enable your site. Ignore warnings about the docroot
```
sudo a2ensite mysite.localhost
sudo service apache2 restart
```

### /etc/hosts

```
#add this to the end of the file:
#this should match your ServerName
127.0.0.1   mysite.localhost
```

### Update Apache

Change priority of index.php to be left-most in `/etc/apache2/mods-enabled/dir.conf`
```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
Restart apache

#### Other tips:
Don't do this in a production environment!

* Change your Apache User and Group to run as your local user (i.e. ubuntu)
    * Update /etc/apache2/apache.conf
    * Replace `User ${APACHE_RUN_USER}` with `User ubuntu`
    * Replace `Group ${APACHE_RUN_GROUP}` with `Group ubuntu`
    * Restart apache

### Test your setup

```
mkdir -p ~/mysite/public
echo "<?=phpinfo()?>" >> ~/mysite/public/x.php
```

Delete this before proceding with your CWP setup
```
rm -rf mysite
```

## Create your CWP project

Assuming all your installation requirements are met, installing the CWP project can be done with a single command:
```
cd ~
composer create-project cwp/cwp-installer mysite ^2
```

This will take a while to run (~10 minutes)

### What's happening here

* We're using composer to install CWP
* `create-project` pulls a Github repository commonly known as a "recipe"
* `cwp/cwp-installer` is the name of this recipe
* `^2` means "install the most recent stable version of major release 2"

### Folder structure
SilverStripe is modular - every piece of code you write is considered to be a module, including the contents of your `app` folder. 
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
The "app" folder contains all of your PHP code and project-specific templates. You'll define any custom pagetypes, models, dataextensions, YML config, and other project-specific stuff in here. **Never** modify modules in the `vendor` folder directly, unless you're pushing changes back to a version controlled project.


#### public
The "public" folder contains all publicly visible assets. This includes documents and images uploaded into the CMS, as well as symlinks to the JS, CSS, imagery, and other assets that are whitelisted by your theme. You will never need to edit anything yourself in this folder. 

SilverStripe serves all requests to the web from the /public folder. All other parts of your codebase are protected from the webserver, which does not require direct publicly-accessible access to them. This is also where the CMS `assets` folder places files uploaded from the CMS.

This entire folder should be readable by the webserver user.

#### themes
The "themes" folder is where your front-end theme (or multiple themes) will live. Your theme will "expose" JS, CSS, and other files to the public folder. Your site can use a single theme, or it can use multiple themes which "cascade" down to a base theme. 

You'll specify a theme in app/_config/theme.yml. In CWP there's currently one loaded by default:

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

`$public` and `$default` are special cases of the theme loader that need to be included for historical reasons. Everything in the `themes` folder is symlinked to `public`, which contains public resources such as CSS, JS, and other web assets like fonts. . `$public` serves as a special type of theme that will take precedence over all others.

`$default` is a fallback theme that all `framework` controllers use to render a skeleton layout

#### vendor
The CWP installer will set up your entire SilverStripe project for you. All required projects are installed to the `vendor` folder and (usually) never need to be modified by the developer. 


### .env file

If you have used previous versions of SilverStripe, you may be familiar with the _ss_environment.php file included with dev projects. This has been replaced with .env text file for SilverStripe 4.

Create a `.env` with the following content:
```
SS_ENVIRONMENT_TYPE="dev"
SS_DATABASE_SERVER="localhost"
SS_DATABASE_NAME="mysite"
SS_DATABASE_USERNAME="root"
SS_DATABASE_PASSWORD="YourRootPassword"
SS_DEFAULT_ADMIN_USERNAME="administrator"
SS_DEFAULT_ADMIN_PASSWORD="TestTest!!!!!!!!"
```

SS_DEFAULT_ADMIN_USERNAME and SS_DEFAULT_ADMIN_PASSWORD create a default administrator user, which you can use to access the CMS for the first time. This should be disabled once you have created an account with a valid email address.

Don't forget to set SS_ENVIRONMENT_TYPE="dev" when you first install. It defaults to "live", which supresses all frontend website errors until changed.

## Building your database
Run the following command from your `mysite` folder:
`vendor/bin/sake dev/build flush=`

You can also visit `http://mysite.localhost/dev/build?flush=` in a web browser. This may require an administrator login.

## Conclusion
At this point, you should have CWP installed with a very basic theme, which is known as the "starter" theme. You can use this theme as a basis for your own custom frontend development, or you can install a new theme and build on top of it. We'll cover this more in a future slide

## Saving your work
The CWP project includes a .gitignore file for files that don't need to be tracked, as well as some sensible defaults for .editorconfig and test suites. 

Now would be a prudent time to create an "initial commit" to your git repository and push it to the remote repository for safekeeping. Make a note of the clone URL for your git repository, it might look like this: 
* git@github.com:you/installing-silverstripe.git
* https://github.com/you/installing-silverstripe.git

```
git remote origin add https://github.com/you/installing-silverstripe.git
git add .
git commit -nM 'Intial commit'
git push -u origin master
```

## Notes and Tips

* On a real server, available to others, such as for your QA and live sites, you would not normally have a default login in the .env file, and especially not admin/pass. So ensure those 2 lines are not present in the file on your live site.
* .env should not be checked in to source control, you would create this file on each server/environment where your website is deployed as the DB connection information will likely be different with a specific user and password for the database of the site (again not root/password either).
* .env should only be used when the site is in `dev` mode. In a production environment, these should be set as system environment variables instead. You can do this with SetEnv in your apache virtualhost.

## Further reading/references

* CWP getting started <a href="https://www.cwp.govt.nz/developer-docs/en/2/getting_started/" target="\_blank">https://www.cwp.govt.nz/developer-docs/en/2/getting_started/</a>


## What's next?
In our next topic, we will show you how to install an already-existing CWP theme, so you don't have to build one for scratch (don't worry, we'll do that too!).

[Lesson 02 - More on Project Structure](02_SiteProjectStructure.md)