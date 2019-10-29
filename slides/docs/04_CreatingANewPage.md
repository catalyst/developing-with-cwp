# Creating a new pagetype


## Creating a new pagetype
In this lesson, we'll demonstrate what PageTypes are and how you can use them. We will first inspect fields inherited from the CWP BaseHomePage page type and explain their purpose. We will learn how to retrieve data from the database, as well as how to set up some relationships to other database tables.


## Before we begin
We are using CWP here to demonstrate how to use a collection of modules to solve a problem well. The lessons learned are applicable to all SilverStripe development, not just CWP.


## Reset your theme to Watea
We're going to continue our lesson using the CWP module. Let's switch back to using the Watea theme:

```
    composer require cwp/watea-theme ^2
    composer require cwp/agency-extensions ^2
```


## Reset your theme to Watea
app/_config/theme.yml
```
---
Name: cwptheme
---
SilverStripe\View\SSViewer:
  themes:
    - '$public'
    - 'watea'
    - 'starter'
    - '$default'
```


## Existing page types

* In this lesson we will create our first new page type for use in the site, but first its probably best to look at what page types come out of the box. CWP adds a lot of them. 

* The best way to see a list of them is to log in to the CMS of our site but putting /admin on the end of the base url, i.e. http://mysite.localhost/admin then log in with "admin" and "password".



### Existing page types
* Ensure the Pages option is selected at the top of the left menu, then click on the "(+) add new" button near the top of the screen.

![Add Page](docs/img/04_add-page.png "Add Page")


### Existing page types
* Here you can see a list of the built in page types, some ones of mention are...


### Existing page types
* Page - this is a normal generic page where content can be entered in a WYSIWYG editor (powered by TinyMCE)
* Event Holder - this page type is provided by CWP and is a listing type of page for events added underneath
* Footer Holder - can be used to control the links displayed in the footer of the site
* Home page - normally only one of these in the site
* News Holder - very similar to event holder but for news


### Existing page types
* Redirector page - redirects users to a specified page in the site, can be helpful in the footer to make the link go to a page in the site, or to manage changes to site structure so users don't get a 404 error, or to allow short urls in social media / marketing for a page deeper in the site
* Error Page - this pagetype allows you to customise the error pages for a variety of different HTTP status codes (Forbidden, Unauthorised, Not Found, Server Error, etc)
* Sitemap page - automatically displays a sitemap of the site, handy to add to the footer via the footer holder


### Existing page types
* User defined form - allows CMS users to create pages containing a form, such as contact us, basic surveys etc.
* Event and News pages - these are for entering the details of the event or news item, they are greyed out because they can only be added under their holder page.


### Existing page types
* Sitemap: a full index of your website, which is useful for accessibility and SEO reasons.
* Blog and Blog Post: commonly used in place of news articles on sites without events


## The landing page
* One main thing which is missing, and that we will be creating now, is a Landing type of page for each section of your website. 
* This page will sit adjacent to the home page in the site tree, but above the general content pages. The plan for this landing page is to list the pages underneath, which provides an easy way for people to navigate your site.


### Creating the landing page

* Normally new page types you create inherit from the "Page" (generic) page type. An easy way to get going is to copy the content from app/src/PageTypes/Page.php into LandingPage.php.  

* Create a new file in your favourite editor and copy and paste in the code from the Page.php into LandingPage.php. 


### Creating the landing page

* Once this is done, in the newly created LandingPage.php file you must edit the class names to include the word Landing, and also change it extend Page for example...


### Creating the landing page

* _app/src/PageType/LandingPage.php_

```php
class LandingPage extends Page {
    private static $db = [];
    private static $has_one = [];
}
```

You will also need to create your controller

* _app/src/Controller/LandingPageController.php_

```php
class LandingPageController extends PageController {
    private static $allowed_actions = [];
}
```


### Page Class and Controller class explained

* These are the _model_ and _controller_ in the model-view-controller paradigm
* The _Model-View-Controller_ design pattern is a common and well regarded way to organise the code structure of a CMS or other PHP framework. 


### Page Class and Controller class explained
* The code for these three things are in separate files:
  + one file for the Model (think of this as the database layer)
  + one file for the View (the code to render the HTML)
  + one file for the Controller (contains the business logic, connects the DB model with the view).


### Page Class and Controller class explained
* In SilverStripe 4, the Model and the Controller classes are split out into separate files - a Model and a Controller.
* In LandingPage.php there are arrays called $db and $has_one.
* The controller, at the moment, contains no methods that will respond to a URL 
  + one exception is the _index_ method, which is inherited from the `Controller` class


### Page Class and Controller class explained
* If we were to add a new action to this controller, we would add it to the "allowed_actions" array to get it to respond via a URL.

* Example:

```php
class LandingPageController extends PageController 
{
    private static $allowed_actions = ['hello'];
    public function hello($request)
    {
        return 'hello';
    }
}
```


### Page Class and Controller class explained

* If your URL was http://mysite.localhost/landing-page/hello, this would summon the LandingPage controller and invoke the `hello` action.
* You could define a `url_handlers` array to redirect other actions to your method: `
```php
private static $url_handlers = [
    'kiaora' => 'hello'
];
```

* We have changed a private static variable - don't forget to flush!


## dev/build, with ?flush=

![Dev Build](docs/img/04_dev-build.png "/dev/build")

* Even though the LandingPage.php file is reasonably bare, it contains the minimum required* for a new page type to appear in the CMS, but only after you run dev/build to update the database. 

* To do this add /dev/build to the base URL of your website in your browser.

<small>Note the $db, $has_one, and $allowed_actions arrays are not actually needed either if you really want just a new page type in the CMS with no additional functionality on top of the page it extended.</small>



### dev/build (cont'd)
* Once that is done return to the CMS.
* Click the "(+) Add new" button in the pages section
* You should see a page type called "Landing Page" in the list of options.


## Adding a description

* You may have noticed in the list of pages in the CMS, there is no description for your page to the right of its name.
* Lets add one to help describe to a CMS user what the type of page is to be used for.
* To do this add the following line inside the LandingPage class on a line before the $db array...

```php
private static $description = 'For each main section of the website';
```


### Adding a description 
* Run dev/build again in your browser
  + we will be doing this a lot so I like to keep a browser tab open with the /dev/build URL
* Reload the Add New Page in the CMS. You will now see the Landing Page in the list, along with the description we provided.


### Adding a description 
![Landing Page Description](docs/img/04_add-landing-page.png "Landing page description")

* You have just created your very first page type.
* But wait... that's only a model and a controller! Where's the view?  More on that soon...


## ORM

* First, let's talk about the ORM and database structure.

* SilverStripe uses an ORM system for the database
* ORM stands for Object Relational Model


### ORM

* You create objects in code such as pages and other dataobjects
* SilverStripe will take care of creating the Database tables for them. 
* As a developer you don't have to design and create DB tables yourself


### ORM

* The ORM system means that as a developer, you can write high level code to create, read, update, and delete. Known as CRUD, these are the four basic functions of database storage. 
* You do not need to write raw SQL or build sql queries, though you can if you need to.
* Uses "Active Record" style syntax


### How to use the ORM

The ORM is an incredibly powerful tool that you can use to query just about anything in the database. Here are some examples:


### How to use the ORM
```php
//fetch a list of all homepage objects, and return the first one
$homepage = HomePage::get()->first(); 

//get all pages, sorted by most recently edited first
Page::get()->sort('LastEdited DESC'); 

//get 10 most recently edited pages, with titles that:
//* Start with A
//* do not end with S
Page::get()
  ->filter('Title:StartsWith', 'A')
  ->exclude('Title:EndsWith', 'S')
  ->sort('LastEdited DESC')
  ->limit(10);
```


### How to use the ORM (continued)
```php
//get page with ID#12
Page::get()->byID(12);

//get all of the QuickLinks on the homepage we queried earlier
$quicklinks = $homepage->QuickLinks();

//how many quicklinks?
$quicklinks->Count();
```


### Advanced ORM and raw queries
* The ORM also supports more advanced features, such as `innerJoin`, `leftJoin` and `where` to write complex queries if required
* These are rare, but there are good reasons for using them, such as performance tuning.
* SilverStripe reports and dashboards often use raw queries where data is complex and the ORM is impractical.



### Using the ORM
* The SilverStripe docs have this very succinct explanation of what the SilverStripe ORM means...
  + Each database table maps to a PHP class
  + Each database row maps to a PHP object
  + Each database column maps to a property on a PHP object


### Using the ORM
* The ORM has many benefits:
  * it makes your development quicker and easier
  * It allows other code to hook in to different events, such as onBeforeWrite or onAfterDelete


### Using the ORM
 You are not restricted to database technologies:
* You can even change the underlying database your site runs on.
* Need to change from MySQL to Postgres? Or MSSQL? or SQLite? No problem!


### Using the ORM
Because SilverStripe creates and maintains the database structure for you, it is necessary to run dev/build after creating, removing, or altering a class or its properties. The database tables and columns stay in sync with what is defined in the code.


## Back to dev/build

You can run the dev/build task by visiting the base url of your site with /dev/build added on the end while logged in to the CMS as an administrator. For example http://mysite.localhost/dev/build

The task compares the current database to the classes defined in code and will perform the changes if necessary


### dev/build
* Create any missing tables
* Create any missing fields
* Create any missing indexes
* Alter the field type of any existing fields
* Rename any obsolete tables that it previously created to obsolete(tablename)


### dev/build

* A few important things to note
  + The task won't delete tables or delete columns from tables no longer used.
  + It will also ignore any tables it does not recognise so long as the names of those tables don't match that of a SilverStripe class
  + This means SilverStripe can co-exist in a DB with tables created by another framework.


### dev/build (caveats)
* Occasionally during development, the /dev/build task will find "conflicts" in your database tables, similar to merge conflicts in your code.
* this is often caused by adding, deleting, or modifying an enum column
* Foreign Key IDs from a has_one or has_many can also cause issues.
* You will need to manually manage these issues through database administration.


### When to dev/build?

* Cases when you need to dev/build are...
  + You have created a new PHP code file, such as a new Pagetype
  + You have made any changes which will affect the database such as adding or removing fields from the $db, $has_one, and some other arrays.
  + You have created (or removed) a template file
  + Changes to any yml file
  + After installing a new module or DataExtension


### When to NOT dev/build?
* Cases when a dev/build is not necessary (but not limited to)...
  + Altering CMS fields in the getCMSFields() function
  + Altering the HTML structure in an existing template file
  + JavaScript or CSS changes

* A simple refresh of the page should be sufficient in these cases, when there are JS or CSS changes a hard refresh (CTL-F5) might be needed.


## Table structure in the SilverStripe database

Now lets have a look at the table structure in a SilverStripe database: 


### General

Most tables in SilverStripe have an auto-incremented ID column, a auto populated Created, and LastEdited datetime fields. They may also have a ClassName field, which represents the class that describes that particular DataObject.


### SiteTree

Arguably the most important table in the database is called SiteTree.

Site tree contains all the basic info about the page records, including the UrlSegment, Title, Content, Sort, ParentID and so forth.


### SiteTree
* If you were to `select * ` all records in the SiteTree table, you will see a number of records with various class names: Page, ErrorPage, HomePage.  

* If you added a Landing Page in the CMS a record with the ClassName of LandingPage is created.

* SiteTree and Pages are only created with a new table if you have specified an additional field for that page type.


### SiteTree

* It will create a 1 to 1 relation between this new table and site tree, then when querying to get all the information about this table it will join the 2 tables together.

* SilverStripe will also inherit information from "child" tables only containing the new columns. This normally requires some complicated database queries, but the ORM takes care of all of this behind the scenes.


### SiteTree

At this stage because we did not add any new fields to the LandingPage class we created, no LandingPage table in the DB has been created. All the information about the LandingPage can be contained in the SiteTree table.

As an example, let's look at [BaseHomePage.php](https://github.com/silverstripe/cwp/blob/2.2.3/src/PageTypes/BaseHomePage.php) for a real example of definitions used by CWP


### Config fields
* We observe these as `private static $variablename;` at the top of the class.
* These provide some sensible defaults for configurable values used throughout the pagetype. 
* Normally, a `private static` variable would mean that PHP can never access its values from outside the class, so what's happening here?


### Config fields
* These values can be overloaded using a YML file defined on your app/_config folder
* In SilverStripe, this is known as the "Config API", and it is a common way to overload and inject values defined in a module without making any modifications to the module itself


### Config fields

* This means you can heavily customise functionality provided by a module without creating a fork or making your own modifications
* This means that you can continue to use Composer to manage the maintanence of upstream modules. 


### Config fields vs Injector
* You can also use something called the _Injector_ to set default values on classes anytime `DataObject::create()` is called. 
* Controlled by YML, the Injector can also call methods and pass in parameters when this method is invoked.
* This is very useful for tests, and required to use some modules.


### A bit about the Injector
* The Injector can also read environment variables from your environment and inject them into your code. 
* This means sensitive information, like passwords and API keys, can be referenced as constants and injected into your codebase
* The Injector is a very complex topic. We will not cover use of Injector today, but you can find more information at the end of these slides.



#### Config fields: special cases
The Config API is a powerful tool that can be used to customise functionality throughout your codebase, including in modules you do not control. However, in SilverStripe development you will see some variables in particular are used more frequently than others. 

Let's go into some common uses of private static variables shown on BaseHomePage.php


### BaseHomePage.php
https://github.com/silverstripe/cwp/blob/master/src/PageTypes/BaseHomePage.php


#### $icon
This variable defines the icon that appears in the CMS Site Tree. If ignored, the default Page icon is used


#### $hide_ancestor
* This variable is used to hide a particular "ancestor" page (i.e. a page your current class is extended from).
* It is normally used to hide pagetypes which are not intended to be published directly (such as BasePage and BaseHomePage).
* It can also be utilised by developers to remove particular page types from the CMS.


#### $hide_ancestor
* For example, if you don't want users to ever publish a VirtualPage, you can set the following on your app/_config/config.yml file without making any changes at all to the CMS module:

```yml
SilverStripe\CMS\Model\VirtualPage:
  hide_ancestor: 'SilverStripe\CMS\Model\VirtualPage'
```


###### $singular_name
This defines the human readable "singular" name of the class ("Home Page"). This is most commonly shown on "Add new ..." buttons in the CMS, the "Add New Page" section of the CMS, and in model administration areas. 

If omitted, the name is inferred from the class name.


###### $plural_name
Similar to the singular_name, this is the "plural" name of the class ("Home Pages") and is normally used in similar contexts. 

As with the singular_name,  the name is inferred from the class name if omitted


###### $table_name
While not strictly required, this variable was introduced in SilverStripe 4 to grant developers control over the generated table name, which gets created on dev/build. Without it, you'll see parts of the namespace included in the name of the generated table. It is highly recommended that developers utilise this variable in namespaced code; it may even be required in future versions of SilverStripe.

It is also useful for connecting your classes to code generated on an older codebase, or tables / views that may be managed outside of SilverStripe


###### $summary_fields
* These are a list of "display fields" shown to the user when the object is managed in a GridField, which we will cover later
  + If the column represents the result of a "data list" fetched as a result of a database query, it is automatically **searchable**, **filterable**, and **sortable** with no other input from you
  + These do not need to be a database column: they can also call a method to display arbitrary data in the cell, such as displaying a ThumbNail or the calculated sum of two or more other columns


##### $summary fields can be calculated
* This is known as a "getter" method, which we'll learn more about later
* When using a getter, you lose the ability to search, sort, or filter on that particular column. However, you can define the data it represents in the *$searchable_fields* array


##### Database relationships
SilverStripe utilises the ORM to extract information from your database without writing any queries at all.

In order for the ORM to work its magic, each class that uses it needs to be told how it relates to other classes


###### $db
* This is an array of fields to database types. Possibly the most commonly used throughout SilverStripe
  + If you were to open the "HomePage" table and pick any database column, you would usually find a corresponding entry on the $db field
  + This is not always the case, as some variables (such as Title, ID, LastEdited, and Created) are inherited from other classes
  + The ORM takes care of all of this for you, and you never need to worry about writing complicated INNER JOIN queries yourself. 


###### $has_one
* This is very similar to the $db field, but it specifically maps to a single ID on another table.
* It always appears in a database column with the suffix ID
* For example, in the future your Home Page may "has one" HeroImage. This creates a HeroImageID on the HomePage table, and refers to an Image class on the File table. 

```php
private static $has_one = ['HeroImage' => Image::class];
```


###### $belongs_to
The belongs_to is a special case for the has_one relationship. This informs the ORM to use the corresponding has_one relationship on the other class to complete the database join
```php
private static $belongs_to = ['Parent' => HomePage::class];
```


###### $has_many
This is demonstrated on the homepage with "Quicklinks" records. In CWP, QuickLinks on the homepage are a series of simple DataObjects, known as "quick links", that relate to hyperlinks on another area of the website. A home page "has many" of these links, and it is managed with a GridField. 


###### $has_many
* It is important to note that "has_many" objects can only have a single parent, so they cannot be reused on other classes.
* If you wanted to re-use your set of Quicklinks on other pages, you would want to use a many_many relationship instead.
```
private static $has_many = ['QuickLinks' => QuickLink::class];
```


###### $has_many requires a $has_one
A corresponding $has_one relationship is required to use a has_many field. This is most commonly used to demonstrate a "Parent" relationship to a child class. For example
```
private static $has_one = ['Parent' => HomePage::class];
```


##### Other fields
* Some other fields available to the developer (not shown on BaseHomePage) are:
  + many_many
  + belongs_many_many
  + many_many_extraFields


###### $many_many
* This is similar to the has_many relationship, but it allows you to re-use records by attaching them to other records without duplication
  + One example of this is the "related pages" feature in CWP
  + The ORM manages the relationship through a "join table" with a set of IDs
  + This is like saying "a blog entry has many tags, and a tag belongs to many different blog entries": many_many would appear on the BlogEntry class.


###### $belongs_many_many 
This is a reciprocal relation on another table to complete the the many_many relationship. This is like saying "a blog entry has many tags, and a tag belongs to many different blog entries": belongs_many_many would appear on the tag class.


###### $many_many_extraFields
It is possible to inject data into your join table, if the relationship requires some extra information. A common example of this application is a "SortOrder" integer field, which tracks the sorted order of IDs in a relationhip. If, for example, your tags were not sorted alphabetically but managed in the CMS, a many_many_extraField might be used to manage that relationship


###### $many_many_extraFields: caution
`many_many` relationships consist of three columns: ID, ParentID, and ChildID. Because of this, duplicates are not possible. You would to add an _extraField "MyCount" on the Child class

```
private static $many_many_extraFields = [
    'Parent' => [
        'Count' => 'Int'
    ]
]
```
This will add a "Count" column to your `Parent_Child` table. The `MyCount` property is accessible on `Parent` and `Child` when referenced in a relationship.



###### $many_many through
This is a new feature introduced in SilverStripe 4 which allow the "internal join table" to be associated with a DataObject. 

This is like saying "A Member has Many verified bank accounts, and a verified bank account can belong to many owners. Go through the SharedBankAccount relation for verification information"


###### $many_many through example
<small class="w-100">
```
class Member extends DataObject {
    private static $many_many = [
        'VerifiedBankAccounts => [
            'through' => SharedBankAccount::class,
            'from' => 'Owner',
            'to' => 'Account
        ]
    ];
}
class BankAccount extends DataObject {
    private static $belongs_many_many = [
        'Owners' => Member::class
    ];
}
class SharedBankAccount extends DataObject
{
    private static $table_name = 'SharedBankAccount';
    private static $db = ['VerifiedDate' => 'Datetime'];

    private static $has_one = [
        'Owner' => Member::class,
        'Account' => BankAccount::class
    ];
}
    ```
</small>


###### $many_many through: example
We can now access the join table directly, when we know the ID for Owner and Account.
```
$accountID = (int) $request->postVar('AccountID');
$ownerID = (int) $request->postVar('OwnerID');

$account = SharedBankAccount::get()
    ->filter([
        'AccountID' => $accountID,
        'OwnerID' => $member->ID
    ])->first();
var_dump($account->VerifiedDate);
```


###### $many_many through: templates
The data on the join table is accessible within a loop:
```
<% loop CurrentUser.VerifiedBankAccounts %>
Verified $Join.VerifiedDate.Ago
<% end_loop%>
```
It is not possible to do this with many_many_extraFields.


## What is a DataObject?

* Simply put, a _DataObject_ is a SilverStripe PHP class that corresponds to a database table.
  + The table will contain a list of these DataObjects and their descendents, also known as a DataList
  + The DataObject class describes the relationship with other DataObject classes and their tables
  + The SilverStripe framework uses an "ORM" (object relational mapper) to abstract the database functionality
  + We'll learn more about this in a bit.


### What is a DataObject?
There are two sibling classes, ArrayList and ArrayData, with similar functionality but does not represent any database output. 


### What is a DataObject?
All pagetypes are extended from DataObject - it is the source of common fields such as ID, Created, and LastEdited.


### What is a DataObject?
The same system of "inheritance" can apply to Dataobjects as well, where if you have a child class of a dataobject you created, a child table will only contain the new fields.


### What is a DataObject?
Note with DataObjects there is no common table like there is with pages and the SiteTree table.


### More details

We will look at the database more throughout this course as we create DataObjects, relations, and more CMS fields. The SilverStripe docs linked below contain heaps of information about the ORM.


### Further reading
* Some helpful links
  + How to create pages in the CMS https://userhelp.silverstripe.org/en/4/creating_pages_and_content/pages/
  + SilverStripe ORM and data model https://docs.silverstripe.org/en/4/developer_guides/model/
  + Intro the ORM https://docs.silverstripe.org/en/4/developer_guides/model/data_model_and_orm/
  + Injector: https://docs.silverstripe.org/en/4/developer_guides/extending/injector/


### Next

[Lesson 05 - Creating a landing page template](05_CreatingATemplate.md)