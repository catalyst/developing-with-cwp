# Creating a module - also, DataObjects and ModelAdmins!

In this section, we will detail how to create a new module, and load it with DataObjects and ModelAdmins that can be dropped onto another project.

You can create your own module in SilverStripe very easily. Quite often this is done to de-couple your code from your project and allow it to be dropped into other projects. Modules can retain their own separate version control history, and are generally maintained separately from your parent project. They're also very important to the open-source ecosystem that SilverStripe depends on - SilverStripe itself is a group of modules, and CWP exists because of community-created modules.

## Folder structure
A module is simply a folder with a particular set of files:

```
yourmodule
    composer.json
    _config.php
    _config/
        config.yml
    src/
        ...
        
```
### _config.php
This is required to exist in order for the module to be detected by the framework. It is almost always empty, but it can be used to initialise parts of your module that cannot be defined in YML. For example, perhaps your module needs a random number generated on each request, or needs to override the memory_limit on PHP using ini_set.

When I say "empty", I actually mean it needs an opening `<?php` tag and a line break. Otherwise your build will fair It does not need to execute any PHP

### config.yml
Once the module detects your _config.php file, it will consult the contents of the `_config` folder for any settings that have been defined in YML. As we observed previously, YML can be used to override `private static $variable` values defined on a class. For example, a module might provide a new DataExtension for some class in the framework, and automatically apply itself so the developer does not have to:
```
SilverStripe\SiteConfig\SiteConfig:
  extensions:
    - 'YourName/YourProject/SomeDataExtension'
```

### src
This suggests, but does not enforce, the PSR-4 PHP specification for autoloading classes. There is no proscribed folder structure other than a top-level folder (usually `src` but some pre-PSR4 modules still use `code`). A typical module will look something like this:
```
src
    Admin/
        SomeModelAdmin.php
    Model/
        SomeDataObject.php
    PageType/
        SomePageType.php
    Extension/
        SomeDataExtension.php
```
### composer.json
It is important to include a composer.json file with your module so that it can be distributed and managed with composer in other projects. Here's an example of one I've written:

```js
{
    "name": "elliot-sawyer/totp-authenticator",
    "description": "Enable 2FA authentication with TOTP",
    "type": "silverstripe-vendormodule",
    "license": "BSD-3-Clause",
    "keywords": [
...
    ],
    "authors": [
        {
            "name": "Elliot Sawyer"
        }
    ],
    "require": {
...
    },
    "require-dev": {
...
    },
    "autoload": {
        "psr-4": {
            "ElliotSawyer\\TOTPAuthenticator\\": "src/",
            "ElliotSawyer\\TOTPAuthenticator\\Tests\\": "tests/"
        }
    },
    "extra": {
        "branch-alias": {
            "dev-master": "1.0.x-dev"
        }
    },
    "support": {
        "issues": "https://github.com/elliot-sawyer/totp-authenticator/issues",
        "source": "https://github.com/elliot-sawyer/totp-authenticator"
    },
    "minimum-stability": "dev",
    "prefer-stable": true
}

```
At a minimum, you should provide the "name" and "type" keys. The `name` key is what composer uses to look up and install your module. `"type":"silverstripe-vendormodule"` ensures the module is installed in the vendor folder, rather than the top-level of your project. 

#### vendor-expose
If any part of your module is publicly accessible (i.e. you've written a Javascript app with a CSS stylesheet and some icons), you'll need to "expose" those directories. You can do this with the `extra` key:
```js
"extra": {
    "expose": [
        "dist/js",
        "dist/css",
        "dist/img",
        ...
    ]
}
```
#### Packagist, and using forks
Your module will only work with `composer require` if you list it publicly on packagist. For a private repository or one you can't/won't publish on packagist, you'll need to add a key called "repositories" after your "require" keys for composer to pick them up:
```js
{
    ...
    "repositories": [
        {
            "type": "vcs",
            "url": "git@github.com:elliot-sawyer/totp-authenticator.git"
        }
    ],
    ...
}
```
Make sure to use the SSH URL for this, otherwise you may need to type or cache a password when running composer.

This feature is also useful for when you've forked another user's module (say, to fix a bug in the CMS). Normally composer will checkout the parent project, but if you provide it with an alternative repository that uses the same "name" key in its composer file, Composer will seek out the version defined in the "repositories" field instead.

## Installing it
Now that you've created a module, how do you ensure it's included in your project? 

The best way is to ensure your project is pushed to your remote version control so that Composer can automatically install it in the `vendor` folder and pick it up on `dev/build?flush=`. If you want to continue making version-controlled chan


## Great - let's build something!

We're going to make a new module called yourname/email-management

Create the following files:
    mkdir -p yourname/email-management/_config
    mkdir -p yourname/email-management/src/Admin
    mkdir -p yourname/email-management/src/Model
    touch yourname/email-management/_config.php
    touch yourname/email-management/_config/config.yml
    touch yourname/email-management/src/Admin/EmailManagementAdmin.php
    touch yourname/email-management/src/Model/ManagedEmail.php
    touch yourname/email-management/composer.json

Now let's make some changes to composer.json so our local composer can find it:

composer.json
```js
{
    "name": "yourname/email-management",
    "type": "silverstripe-vendormodule",
    "require": {
        "silverstripe/framework": "^4"
    }
}
```

config.php (note the newline character after the `<?php ` tag)
```
<?php

```

1. Initialise a new git repo `git init` and do an initial commit `git add .; git commit -m 'initial commit'`
2. Create a new remote repository on github or similar, make note of the clone URL.
3. Add a new remote to your newly initialised repo: `git remote add origin <yourcloneurl>`
4. Force push your new repo to it `git push -u origin master`
5. Update the composer.json file in your parent repository to include this new repo:
```js
    "require": {
        "yourname/email-management":"dev-master as 0.0.1"
    },
    "repositories": [
        {
            "type": "vcs",
            "url": "yourcloneurl"
        }
    ]
```
6. Run `composer update`. You should see your new module pulled into the project.

Congratulations: you've now built and published a new module, and included it in your CWP website. Let's add some stuff to make it useful.

### The Managed Email DataObject
As a developer, I find there are 2 types of things I create the most when building SilverStripe websites. The first are Pages (which we have already seen), the other is DataObjects which are also a class with properties and methods. Data objects are more low level, not as featured as pages. In fact if you follow the inheritance tree back far enough you will discover that pages are actually children of the DataObject class.

Unlike a page which represents a whole page in the site and contributes to the structure by appearing in the site tree. Dataobjects are useful containers for smaller bits of functionality which in many cases may not be that visual (and therefore don't have a corresponding template in the theme), and are not normally structural like a page.

For example, in this lesson we are going to create a Data Object for managing emails. This will allow us to write and manage email messages in the CMS, including the From Address, a To: address, the Subject, and the Message body. We will utilise this later when learning how to build forms

Create a new file in src/Model/ called ManagedEmail.php
```php
namespace YourName\ManagedEmails;
use SilverStripe\Control\Email\Email;
use SilverStripe\ORM\DataObject;
class ManagedEmail extends DataObject
{
    private static $db = [
        'Label' => 'Varchar(255)',
        'FromAddress' => 'Varchar(255)',
        'ToAddress' => 'Varchar(255)',
        'Subject' => 'Varchar(255)',
        'Body' => 'HTMLText'
    ];

    private static $summary_fields = ['Label', 'Subject'];

    public function send($toAddress)
    {
        $email = Email::create();
        $email->addTo($toAddress);
        $email->addFrom($FromAddress);
        $email->Subject = $this->Subject;
        $email->Body = $this->Body;

        $email->send();
        
    }
}
```

#### Validation
Let's make the "From" address required. This is important, as emails cannot be sent at all unless the sender is defined. Add the following method after the `send` method:
```php

    public function validate() {
        $valid = parent::validate();

        if(!$this->FromAddress) {
            $result->addError('The "From" address is required');
        }
        return $valid;
    }
```

Now if the From address is missing, we won't be able to save our DataObject until we fix it.

### The Emails ModelAdmin

At present if you go to the CMS and look for your Managed Email dataobject you won't find a way to create them. This is another big difference between DataObjects and Pages; pages can always be created via the Site Stree, but DataObjects have no singular place for creation by default. This is because they are often used on specific pages, so you would go to that page and create some data objects. An example is the home page of the site, once its page type is changed from just "page" to "Home page" you can add Quicklinks to it. These quicklinks are a dataobject provided by CWP.

We want to create a place in the CMS for our Managed Emails to be viewed, created, updated and deleted without having to add them to a page. We will add this to our module but by creating a _Model Admin_. As the name suggests this feature provides a way to administer a set of models. We will create one of these now so we can manage our Managed Emails for use in a form that we will create later.

ModelAdmins are dead simple to create. Create a new file src/Admin/ManagedEmailAdmin.php
```php
namespace YourName\ManagedEmails;
use SilverStripe\Admin\ModelAdmin;
class ManagedEmailAdmin extends ModelAdmin {
    private static $menu_title = 'Managed emails';
    private static $url_segment = 'managed-emails';
    private static $managed_models = [
        ManagedEmail::class
    ];
}
```

That's it: these three fields are all that's required to add a new admin area to the CMS, with a new tab and a GridField for managing your models. You can created, retrieve, update, and delete any of your ManagedEmail records. You can import and export existing records to and from a CSV, search and filter for emails, and sort them by any column you want.

You may have noticed that the `$managed_models` variable is an array - you can add any number of models to administer in this area, and a new tab will be created for each one.

# Summary

In this lesson, we have learned quite a bit:
1) How to create a new module from scratch
2) How to define new DataObjects, and
3) How to administer any DataObject using a ModelAdmin

# Further reading/references

* SilverStripe add ons http://addons.silverstripe.org/
* Modules dev docs https://docs.silverstripe.org/en/4/developer_guides/extending/modules/
* SilverStripe Data model and ORM https://docs.silverstripe.org/en/4/developer_guides/model/data_model_and_orm/
* Model admins https://docs.silverstripe.org/en/4/developer_guides/customising_the_admin_interface/modeladmin/
# Next

[Lesson 08 - Writing extensions](08_WritingDataExtensions.md)