# Extending your modules
## Extensions and DataExtensions


### Extensions and DataExtensions
An extension is a PHP class that "decorates" the classes in other modules with additional functionality. We can create an Extension subclass in our own project code to enhance or change the behaviour of other modules.


### Extensions and DataExtensions
* We'll use this concept to achieve three objectives:
    - adding a method to an existing controller or DataObject
    - adding new database columns
    - adding CMS fields with an extension hook


### Extensions and DataExtensions
* What extensions can't do:
    - replace existing methods, or remove them
    - remove existing database columns (but it can remove their CMS fields)
    - modify methods without a hook


### Aren't these just traits?
* Sort of, but no. DataExtensions offer more than a trait can:
    - Traits are useful for repetitive code within your own project
    - Traits cannot access private static variables in their implementing class
    - Traits cannot be used in other modules without modifying the codebase


### Using a DataExtension to add new methods
We'll add a new action that is accessible to all published userforms

Add the following file to app/Extension/KiaOra.php
```php
use SilverStripe\ORM\DataExtension;
class KiaOraExtension extends DataExtension
{
    private static $allowed_actions = ['kiaora'];
    public function kiaora($request) 
    {
        return 'Kia ora!!';
    }
}
```


### Using a DataExtension to add new methods
Add this to app/_config/config.yml
```yml
SilverStripe\UserForms\Control\UserDefinedFormController:
  extensions:
    - 'KiaOraExtension'
```


### Using a DataExtension to add new methods
Run a `/dev/build?flush=` and visit a userform. If you haven't published one, then do so now and visit its URL. add /kiaora to the end of its URL and observe the result


### Using a DataExtension to add new methods
We've added our own method to the userforms controller that returns any data that we want, all without modifying the controller in the vendors folder. As a page with an action, we can use the CMS to link directly to it. The allowed_actions are merged with those of the controller we're extending. 

Conflicts are ignored, and you cannot remove existing allowed_actions from the array.


### Using a DataExtension to add new methods
You cannot use an extension to replace an existing method.

However, if that existing method defines an _extension hook_, we can modify its result before it is returned.


### Using a DataExtension to add new methods
The author of the module needs to allow an extension hook:
```php
$this->extend('nameOfExtensionHook', $dataToModify)
```
within the defining method. 

Your extension implements `nameOfExtensionHook($dataToModify)` and manipulates the $dataToModify variable.

`extends` is a no-op unless there's an extension defined. Raise a pull request if you need one on a module.


### Using a DataExtension to add new methods
The `$dataToModify` is passed in as a reference - this means PHP utilises the actual variable from the parent object rather than working with a copy. For this reason, the method on the extension does not need to return anything. 


### Adding new Database columns and fields
We'll write an extension to add a CopyrightStatement field to the SiteConfig, which implements an extension hook on its `getCMSFields` method (in fact, all DataObjects provide this hook)


### Adding new Database columns and fields
Create app/Extension/SiteConfigExtension.php:
```php
use SilverStripe\ORM\DataExtension;
use SilverStripe\Forms\TextField;
class SiteConfigExtension extends DataExtension
{
    private static $db = [
        'CopyrightStatement' => 'Varchar(80)'
    ];
    public function updateCMSFields($fields)
    {
        $fields->addFieldToTab(
            'Root.Main',
            TextField::create('CopyrightStatement')
        );
    }

    public function DisplayCopyright()
    {
        return $this->owner->Title . $this->owner->CopyrightStatement;
    }
}

```


### Adding new Database columns and fields

Add the following to app/_config/config.yml

```yml
SilverStripe\SiteConfig\SiteConfig:
  extensions:
    - 'SiteConfigExtension'
```

* Extensions are so commonly used, developers will often place all extensions in their project in their own _extensions.yml_ file. This is completely optional


### Adding new Database columns and fields
Like the controller above, we write an extension to augment the SiteConfig DataObject found in another module we do not control. The extension has provided a new database field with its own $db array, and utilised an _extension hook_ to update the CMS fields of that DataObject.


### Adding new Database columns and fields
Note the use of `$this->owner` to refer to the database fields instead of `$this`, which an important distinction with DataExtensions. Everything the extension adds will _always_ apply the the parent rather than the extension itself, with one exception: `private static $variables` applied via config. If you need to overload these values using the Config API, refer to the extension instead.


### Adding new Database columns and fields
The `DisplayCopyright()` method is added to the SiteConfig object, which means its also accessible through the template.  Add the line `$Top.SiteConfig.DisplayCopyright` anywhere in one of your templates to see this in action.


## What we've learned

Extensions are a powerful method of augmenting existing modules without making any changes to them. They are a necessary tool for working with modules in SilverStripe.


## Further reading
* https://docs.silverstripe.org/en/4/developer_guides/extending/extensions/


## Next
[Lesson 09 - Writing forms](09_WritingForms.md)