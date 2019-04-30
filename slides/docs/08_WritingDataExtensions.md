# Extending modules with Extensions and DataExtensions

## What is an extension?

An extension is a PHP class that "decorates" the classes in other modules with additional functionality. We can create an Extension subclass in our own project code to enhance or change the behaviour of other modules.

We'll use this concept to achieve three objectives:
- adding a method to an existing controller or DataObject
- adding new database columns
- adding CMS fields with an extension hook

What it can't do:
- replace existing methods, or remove them
- remove existing database columns (but it can remove their CMS fields)
- modify methods without a hook

### Adding new methods
We'll add a new action that is accessible to all published userforms

Add the following file to app/Extension/KiaOra.php
```php
use SilverStripe\ORM\DataExtension;
class KiaOra extends DataExtension
{
    private static $allowed_actions = ['kiaora'];
    public function kiaora($request) 
    {
        return 'Kia ora!!';
    }
}
```

```yml
SilverStripe\UserForms\Control\UserDefinedFormController:
  extensions:
    - 'KiaOra'
```

Run a `/dev/build?flush=` and visit a userform. If you haven't published one, then do so now and visit its URL. add /kiaora to the end of its URL and observe the result

We've added our own method to the userforms controller that returns any data that we want, all without modifying the controller in the vendors folder. As a page with an action, we can use the CMS to link directly to it. The allowed_actions are merged with those of the controller we're extending. 

You cannot use an extension to replace an existing method. However, if that existing method defines an _extension hook_, we can modify its result before it is returned. The author of the module needs to allow it using `$this->extend('nameOfExtensionHook', $dataToModify)` within the defining method, and your extension implements `nameOfExtensionHook($dataToModify)`. There's usually not a problem with raising a pull request to do add this line, as it is effectively a no-op unless there's an extension defined. The `$dataToModify` is passed in as a reference - this means PHP utilises the actual variable from the parent object rather than working with a copy. For this reason, the method on the extension does not need to return anything. 

### Adding new Database columns and fields
We'll write an extension to add a CopyrightStatement field to the SiteConfig, which implements an extension hook on its `getCMSFields` method (in fact, all DataObjects provide this hook)

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

```yml
SilverStripe\SiteConfig\SiteConfig:
  extensions:
    - 'SiteConfigExtension'
```

Like the controller above, we write an extension to augment the SiteConfig DataObject found in another module we do not control. The extension has provided a new database field with its own $db array, and utilised an _extension hook_ to update the CMS fields of that DataObject.

Note the use of `$this->owner` to refer to the database fields instead of `$this`, which an important distinction with DataExtensions. Everything the extension adds will _always_ apply the the parent rather than the extension itself, with one exception: `private static $variables` applied via config. If you need to overload these values using the Config API, refer to the extension instead.

The `DisplayCopyright()` method is added to the SiteConfig object, which means its also accessible through the template.  Add the line `$Top.SiteConfig.DisplayCopyright` anywhere in one of your templates to see this in action.


## What we've learned

Extensions are a powerful method of augmenting existing modules without making any changes to them. They are a necessary tool for working with modules in SilverStripe.

## Further reading
* https://docs.silverstripe.org/en/4/developer_guides/extending/extensions/

## Next
[Lesson 09 - Writing forms](09_WritingForms.md)