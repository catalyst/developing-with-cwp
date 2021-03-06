# Introduction to CMS fields



## Introduction to CMS fields
In this lesson I will introduce you to the database model and how to add fields in to the CMS for users to enter some information. We will then utilise these new fields on the landing page to make it even better.


### Adding an Intro field to Page.php

The first thing I want to do is to add an Intro field for all pages, above the Content field in the CMS. This intro field will be used for a short description about the page which can then be used on the landing page, the page itself, and other places in the site.


### Adding an Intro field to Page.php
The intro field will be plain text, and have a shorter length that the Content field so there will be no issues about HTML stripping like there is with Content.LimitCharacters.


### Adding an Intro field to Page.php
The steps to adding the field are 2 fold, the first is to add is as a property of the Page class using the $db array, the next is adding a field to the CMS for the user to enter the information, so..


### Adding an Intro field to Page.php
* Open app/src/PageType/Page.php
* In the page class add the intro field to the $db array like this...

```php
private static $db = array(
    'Intro' => 'Varchar(255)',
);
```


### Adding an Intro field to Page.php
The first part, in the single quotes is the field name, in this case 'Intro', the second part after the double arrow operator => is the field type in the database to use, so in this case a Varchar of 255 characters in length.


### Adding an Intro field to Page.php
You can see all of the available database types on this page: https://docs.silverstripe.org/en/4/developer_guides/model/data_types_and_casting/ as you can see there are various types for numbers, text, etc - we will look at more of these later.


### Adding an Intro field to Page.php
Now run dev/build, in the list on the dev/build page you should see some green to indicate that some fields have been created, but this is only in the database. If you load one of the Child pages of the Attractions page then you will not see any new field in the CMS yet to actually enter this information.


### Adding the CMS field

Now lets add a field in the CMS for users to actually enter the Intro for the pages. To do this we have to add a method called getCMSFields() to the Page model class (not the controller. Add the following to your Page.php in the Page class under the $db and $has_one arrays


### Adding the CMS field
Example: 
```php
public function getCMSFields()
{
    // Get parent fields.
    $fields = parent::getCMSFields();

    // Add intro before the content.
    $fields->addFieldToTab(
        'Root.Main',
        TextareaField::create('Intro', 'Intro'),
        'Content'
    );

    // Return the fields.
    return $fields;
}
```


### Adding the CMS field: Explained
We always call the function from the parent class to get any CMS fields it creates. This provides us with the page name, url segment, Content and other fields and saves us having to re-define these. 

Page inherits all of this from BasePage, which inherits it from SiteTree. Any pages that extend Page will inherit these fields through a change of calls to `parent::getCMSFields()`;


### Adding the CMS field: Explained
* Next we add a field for the Intro to the fields. 
* We must specify what tab we want it in: Root.Main is the main tab displayed when the user first goes to the page
  + this is a great place for all of the main, important information which must be entered for the page.
  + You can put fields on other tabs too which might make sense in some cases for less important things, or to group sets of fields together rather than putting everything on the main tab and making the page in the CMS very long.


### Adding the CMS field: Explained
The last parameter of the addFieldToTab 'Content' simply tells the CMS to put the Intro field before the Content field, so when filling out the form the CMS user will enter the Page name, url etc, Intro, then Content which makes sense.


### Adding the CMS field: Explained
The last line of the function returns the $fields array to the code which called it. This will be code inside the SilverStripe cms folder in the root directory of your site. At this point the $fields array will contain all the fields from the parent classes plus the TextareaField we added to the main tab before the Content field.


### Adding the CMS field: Explained
Do a dev/build and then reload one of the Attractions pages, you should now see a field to enter an intro. Please do so for each of the attraction child pages, Saving and Publishing as in the next bit we will go back and alter the Landing page template file.


### Updating LandingPage.php
In the previous step, we added an Intro field to the Page object. Now let's open LandingPage.php and add some more database fields specifically for the LandingPage:

Add two new fields to your $db array:
```php
private static $db = [
  'SpecialContentHeadline' => "Varchar(64)",
  "SpecialContent" => "HTMLText"
];
```


### LandingPage: Add some new CMS fields to a tab
To insert new CMS fields for us to edit, we need to define a method and get the fields from the parent class. We will then add our new fields, and add them to a new Tab on the HomePage record.


### LandingPage: Add some new CMS fields to a tab
Example:
```php
use SilverStripe\Forms\HTMLEditor\HTMLEditorField;
use SilverStripe\Forms\TextField;
public function getCMSFields() {
  $fields = parent::getCMSFields();

  $fields->addFieldsToTab(
    'Root.SpecialContent',
    [
        TextField::create('SpecialContentHeadline'),
        HTMLEditorField::create('SpecialContent')
    ]
  );

  return $fields;
}
```


### LandingPage: Add some new CMS fields to a tab
This is an excellent example of inheritance in action: LandingPage::getCMSFields obtains fields from Page::getCMSFields and injects its own set of fields into the result. This means that LandingPage automatically receives the "Intro" field without any additional development on our part.


### LandingPage: Add some new CMS fields to a tab
Do a `dev/build?flush=` and open your LandingPage in the CMS.  You will now see three new fields:
* Intro, which was inherited from Page.php
* A new tab called "Special Content"
* In "Special Content", a single ToggleCompositeField, which reveals two new fields once clicked:
    * A TextField called "Special Content Headline"
    * A WYSIWYG editor called "Special Content"


### Make a getter method
* Inside our special content area, we want to display the current date and time at the bottom of the section
* This is not database content - it's some other arbitrary data that we can query from any source and display the result
* We can do this by defining a _getter method_, which can be summoned from the HomePage template and and class that descends from it:


### Make a getter method
Example
```php
public function getCurrentDateTime()
{
  return DBDatetime::now();
}
```


### Make a getter method
Getters are commonly used to fetch the result from something not defined in the database, such as the output of a cached API request.

Here, we will use it to fetch the current date and time from the database server.


### Updating LandingPage.ss
In Layout/LandingPage.ss let's display the $Intro field of the children pages, instead of Content.LimitedCharacters


### Updating LandingPage.ss
Example
```html
<% loop $Children %>
    <div>
        <h3>$Title</h3>
        <p>
            $Intro
            <a href="$Link">Read more...</a>
        </p>
        <hr />
    </div>
<% end_loop %>
```


### Updating LandingPage.ss
Also in Layout/LandingPage.ss, add the following before the `<% loop Children %>` segment of your template:
```html
... place at the bottom of the "section" tag containing $Content"...

  <% if SpecialContentHeadline && SpecialContent %>
    <h3>$SpecialContentHeadline</h3>
    <div>$SpecialContent</div>
    <hr />
    <p class="text-center">$CurrentDateTime</p>
  <% end_if %>

```


### Updating LandingPage.ss
Now visit the Attractions landing page in the front-end of you website. You might have noted that I did not say you needed to dev/build, that's because you do not; you can make changes to existing templates and they will appear after refresh without dev/build(ing). The intro you entered for the child pages will now be output.


### Updating LandingPage.ss
If you have not entered anything in the SpecialContentHeadline **and** the SpecialContent fields, you will not see any special content or the timestamp.

You'll note the timestamp shows a very detailed version of the current time. Because this is the output of the DBDatetime class, all of its public methods are available for use within the template. 


### Updating LandingPage.ss

To only show the four-digit year, for example, use `$CurrentDateTime.Format('yyyy')` instead. More information on the possible accepted formats is here: http://userguide.icu-project.org/formatparse/datetime.  Save this URL... you'll use it a lot!

Note that CurrentDateTime is an alias of `getCurrentDateTime`. The `get` prefix is optional, but often preferred to distingush it from your database fields. It can also be used to overload the value retrieved from the database.


## Summary

Well done! This concludes the lessons on how to create a page type in SilverStripe; you created all three parts of the MVC - with the Page (model) class, the page_controller, and also the template view for it. Also you learnt how to add fields in to the CMS for content authors to enter information.

Pages are a very big and important part of SilverStripe sites, you will be creating and extending pages and their templates a lot as a SilverStripe developer.


### Summary
* In the previous lesson and this one, we've learned what a DataObject is, and that a page type is a type of DataObject.
  + We've learned about the ORM and how to use it
  + We've seen how to utilise special variables on our DataObject to set up relationships with other data, and what possible relationships exist
  + Finally, we've learned how to inherit fields from parent pages in the CMS, how to set up getter utility methods, and how to display all of these new concepts on a template for your users.


### Further reading

* SilverStripe data model types https://docs.silverstripe.org/en/4/developer_guides/model/data_types_and_casting/
* SilverStripe Form field types https://docs.silverstripe.org/en/4/developer_guides/forms/field_types/


### Next
[Lesson 07 - Installing a Module](07_InstallingAModule.md)