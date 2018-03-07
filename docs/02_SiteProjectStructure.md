# SilverStripe project structure

In this lesson we will look at the code structure of the site/project. Lets open the root directory of the site in Atom so we can browse the folder and file structure.

SilverStripe is modular so everything is a module, including the core framework, cms, and themes. Probably the only folder not a module is "mysite".

You will see a lot of folders in the site structure, most you do not have to worry about, these are the modules that give CWP SilverStripe is functionality and features. Many are developed and supported by SilverStripe themselves, some are 3rd-party modules in that they are created by other people in the SilverStripe community. In the future one or 2 of these directories might be for modules you have created.

## Folders to note in the directory are

* cms
* cwp
* cwp-*
* framework
* mysite
* themes

The CMS and framework directories contain the core SilverStripe framework and the CMS, all the CWP directories contain functionality on top of the core SilverStripe to give us the CWP version of SilverStripe. If you were to download and install the standard version of SilverStripe then you won't get the CWP directories as well as a number of other modules.

Mysite and Themes are the only 2 where you will be writing code to create new pages and features in the site, all the others are ignored by Git so are not checked in to your repository. You can confirm this by looking at the listing of files in Github for your project. Notice how there is only mysite and some other files.

When you clone down your project to another computer, typing "composer install" will install all the other modules for your site to that machine. Composer knows what to do from the composer.lock (and associated composer.json) file in the root directory of your site, it tells composer which modules and also which version to use. This ensures that when composer install is run on another computer that machine will git the same version of the modules you used for development on your machine, so there should be no issues because of any changes to those modules - because they have not changed.

The other reason I mention the cms, framework, and cwp directories is that sometimes if you have made a mistake in your coding, the website will throw an error from a file in one of these directories. Sometimes if the error message is not detailed enough it is helpful to look at this code to work out why it is not happy and what you need to do to satisfy it.

Also when it comes time to write extensions, its helpful to dive in to these directories and see what fields and functions things like the CWP news and events pages have, along with their class name so you can extend successfully.

# Mysite directory

We will be spending a lot of time in this directory in this course, this is where you create new page types, can create dataobjects (which we will cover later) and other things. The templates for these pages which control how they look and what is displayed to the user are created under the themes folder which we will take more of a look at in the next lesson. While it is possible to include some templates under this folder in the mysite this is not recommended (but can be used in special circumstances).

Things of note in the mysite directory are..

* _config/config.yml
* code
* _config.php

The other directories and files in the mysite can be ignored as we will not need to alter them in this course.

## Preparing the mysite/code directory

Lets prepare the mysite/code directory for the upcoming lessons in this course, please create these folders inside the mysite/code folder. You can right-click the mysite/code folder in Atom and choose "New Folder"...

* admins
* models
* pagetypes

Then on the command line issue these commands to move the HomePage.php and Page.php files in to the pagetypes directory in a way that Git knows about..

```
git mv mysite/code/HomePage.php mysite/code/pagetypes/HomePage.php
git mv mysite/code/Page.php mysite/code/pagetypes/Page.php

git commit
git push
```
# _config/config.yml

For now just note where this is. Some modules have configuration options which are set by a developer, if so you enter this here. This file will also be used in the advanced developer course when we come to do extensions.

# _config.php

This is another configuration file, as noted in the comment at the bottom, normally any SilverStripe config goes in the config.yml and not this file, however there are times when some PHP powered configuration is needed before the framework loads, for example this is why the SS_DATABASE_NAME etc is mentioned in this file.