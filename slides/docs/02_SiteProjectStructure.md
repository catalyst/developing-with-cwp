# Project structure


## SilverStripe project structure

* In this lesson we will look at the code structure of the site/project. Lets open the root directory of the site in Atom so we can browse the folder and file structure.

* SilverStripe is modular so everything is a module. Everything in the `vendor` folder is a module, and your themes are a special type of module. Even `app` is a module, though most people don't consider it to be one. 


### Folders to note in the directory are

```
app
public
themes
vendor
```


### Folder structure: cms and framework
* The CMS and framework directories live in vendor/silverstripe/framework and vendor/silverstripe/cms
* These contain the core SilverStripe framework and the CMS
* All the CWP directories (found in vendor/cwp) contain functionality built on top of the core SilverStripe to give us the CWP version of SilverStripe


### Folder structure: cms and framework
* If you were to download and install the standard version of SilverStripe then you won't get the CWP directories as well as a number of other modules.


### Folder structure: apps and themes
* `app` and `themes` are the only folders where you will be writing code to create new pages and features in the site. All the others are ignored by Git so are not checked in to your repository.  You can confirm this by looking at the listing of files in Github for your project. 


### Folder structure: vendor
* As mentioned before, you should **never** modify the contents of the vendor folder unless you're pushing code back to a parent project or a fork, and you've pointed composer at that fork. 
* Composer manages the contents of this folder, and you will lose work if you're not careful. 


### Folder structure: vendor and composer.lock
* When you clone down your project to another computer, typing "composer install" will install all the other modules for your site to that machine
* Composer knows what to do from the composer.lock (and associated composer.json) file in the root directory of your site, it tells composer which modules and also which version to use


### Folder structure: vendor and composer.lock
* This ensures that when composer install is run on another computer that machine will git the same version of the modules you used for development on your machine, so there should be no issues because of any changes to those modules - because they have not changed.


### Folder structure: vendor
* Sometimes if you have made a mistake in your coding, the website will throw an error from a file in one of these vendor directories
* Sometimes if the error message is not detailed enough it is helpful to look at this code to work out why it is not happy and what you need to do to satisfy it.


### Folder structure: vendor
* Also when it comes time to write extensions, its helpful to dive in to these directories and see what fields and functions things like the CWP news and events pages have, along with their class name so you can extend successfully.


### Folder structure: app
* We will be spending a lot of time in this directory in this course, this is where you create new page types, can create dataobjects (which we will cover later) and other things
* The templates for these pages which control how they look and what is displayed to the user are created under the themes folder which we will take more of a look at in the next lesson


### Folder structure: app
* While it is possible to include some templates under this folder in the `app` folder, this is not recommended
* This is because templates in your app folder take precedence over those found in the theme or vendor folders and thus cannot be modified by any other code
* This can be useful in some circumstances but should generally be avoided by developers.


### Folder structure: app
* Things of note in the app directory are..
```
_config
    config.yml
src
    ...
    ...
    ...
_config.php
```
* The other directories and files in the app folder can be ignored as we will not need to alter them in this course.


## Preparing the app/src directory

* Lets prepare the app/src directory for the upcoming lessons in this course, please create these folders inside the app/src folder.

* You can right-click the app/src folder in your IDE and choose "New Folder"...

```
src 
    Admins
    Models
    PageTypes
```


### Preparing the app/src directory
* Then on the command line, from the root directory of the museum site, issue these commands to move the HomePage.php and Page.php files in to the PageType directory in a way that Git knows about..


### Preparing the app/src directory
```
git mv app/src/HomePage.php app/src/PageTypes/HomePage.php
git mv app/src/Page.php app/src/PageTypes/Page.php

git commit
git push
```

* If not using Git then you can just click, drag and drop, the 2 files in to the pagetypes folder in your IDE to move them.


### Preparing the app/src directory
* Now we need to dev/build the SilverStripe site so SilverStripe updates its information about where the files are so the site still works in your web browser. Add /dev/build to the base url of your website in a web browser.

```
http://mysite.localhost/dev/build
```


## _config/config.yml

* For now, just note where this is.
* Some modules have configuration options which are set by a developer, if so you enter this here.
* This file will also be used in the advanced developer course when we come to do extensions.


## _config.php

* This is another configuration file, as noted in the comment at the bottom, normally any SilverStripe config goes in the config.yml and not this file
* There are times when some PHP powered configuration is needed before your project can begin which cannot be applied via the YML files.
* An example might be increasing the memory_limit or execution_time with ini_set().  


## Further reading

* SilverStripe developer docs https://docs.silverstripe.org/en/4/
* SilverStripe configuration https://docs.silverstripe.org/en/4/developer_guides/configuration/


## Next

* [Lesson 03 - Creating a Theme](03_WorkingWithThemes.md)