# Creating a New Landing Page Template


## Creating a New Landing Page Template

In the last lesson, we did basically the minimum to create a new page type in the CMS; we created the Model and the Controller classes part of the MVC design pattern. In this lesson we will discuss how the page templates the V part work in SilverStripe.


### Creating a New Landing Page Template
If you have not done so already, actually create a new page of the type Landing Page by going to the Pages section in the CMS, clicking "(+) Add New" at the top, then selecting Landing Page.


### Creating a New Landing Page Template
Set the page name to "Attractions" then save and publish the page.


### TIP - turning the preview off
By default the preview pane is turned on the SilverStripe CMS, I don't like it as sometimes it can lie (as in not be a 100% accurate reflection of how the page looks in the front end), and also because with multiple panes things on the screen can get a little cramped.


### CMS Preview
To turn the preview pane off, we can swap from Split Mode to Edit mode using a little Mode Switcher control down at the bottom-right of the screen. Hover over the icons down there an you should see a tool tip on one saying "Change View mode". Click on it to bring up a popup menu, then choose "Edit mode" from the list.

![Mode Switcher](docs/img/05_mode-switcher.png "Mode Switcher")


### CMS Preview
Now things should look a bit less cramped. You can still check how the page looks in the front-end of the site very easily by clicking on the URL segment field under the Page name near the top of the screen, this will open the page in a new browser tab and give you a 100% accurate, desktop sized look at the page, just now it will appear to users of the site when the page is published.


## Back to templates

* As you should have seen when looking at the Landing Page you created called Attractions, it displays fine in the front-end of the site. The menus, title, content, and footer were all output, but why? We did not create a template file for this page type yet.

*  Templates in SilverStripe follow a convention. If a template for a class is missing, it will fallback to the template for the nearest ancestor class (Page.ss) if a specific page template is not found.


### Back to templates
* If no template is found in your project's templates folder, the current theme is checked.
* If no template is found in the current theme, subthemes are checked in descending order
* If no template is found in any theme, the templates directory of modules in the vendor folder is checked
* If there's still no theme found (including in CMS or framework), an error is thrown


## Layout/LandingPage.ss

Normally for each type of page you create in the site, as well as the PHP file with the Model and Controller Classes, you create a template (view) file with a .ss extension because normally you want different types of pages to display different things on them.

For example on the landing page, as well as the Content entered in the TinyMCE editor, below this we want to list the pages in that section of the site, allowing the user to click through to them. We might also want other things on landing pages which do not appear on generic content pages.


### Layout/LandingPage.ss

* Let's create the template file. This is where the themes directory and the copy of the standard theme you created earlier comes in to play.
    + Go in to the themes/museum/templates/Layout directory
    + Create a new file (in Atom right-click on the Layout folder and choose 'New file')
    + Call it LandingPage.ss


### Layout/LandingPage.ss
* It's very important the filename matches the name of the PHP class you want the template to be for. If the names do not match then the template will not be detected or used for the page. 
* If the class exists inside of a namespace, you will need ot use folders to mimic that space: (i.e. templates/YourNamespace/YourProject/PageType/Layout/SomePage.ss)

* Now we can add the HTML to the template, but first...  


## Page.ss vs Layout/Page.ss

* There are 2 files called Page.ss in play in a SilverStripe site. 
    + The first, located in templates/Page.ss, defines the structure for all pages in the site. It contains the opening HTML tag, the head section, includes the Header and Navigation. The footer, Google Analytics code, and closing HTML tags are output.
    + It _then_ includes the $Layout


### Page.ss vs Layout/Page.ss
* The other Page.ss file is in templates/Layout/Page.ss.
    + It is layout for the generic Page type.
    + The HTML defined in this file is included in the first Page.ss where the $Layout variable is located when the site is rendered
    + If you think of it as a sandwitch, the top-level Page.ss is the bread and the Layout/Page.ss is the filling
    + Layout represents the content of our Page or one of the Page subclasses.


### Page.ss vs Layout/Page.ss vs Layout/LandingPage.ss
Similarly for the Landing Page the LandingPage.ss template will be rendered where the $Layout variable appears in the Page.ss


### Adding the needed HTML markup to the LandingPage.ss
 
* To start, it is best to open themes/watea/templates/Layout/Page.ss and copy everything into the LandingPage.ss file.
    + This will ensure we have the correct divs and classes for the theme we are using as the base for our theme


### LandingPage.ss markup
```html
<div class="row">
    <section class="col-md-7 col-md-offset-1">

        <p><strong>This is the landing page template</strong</p>

        <% if $Content.RichLinks %>
            $Content.RichLinks
        <% end_if %>
    </section>
</div>
```


### LandingPage.ss markup
Now save the changes to this file. Time for another dev/build, because the list of existing templates is cached and we need to rebuild it.

Now visit the Attractions Landing Page you created earlier, should see in bold before the Content field, which you entered in the CMS editor. "This is the landing Page template". This proves that the site is detecting the template file we created. We will replace this line in the next lesson.


### Listing child pages

First in the cms we should add 3 child pages, of the type (generic) Page. For now simply give them a name of Child 1, Child 2, and Child 3. Give each one some content in the Content field. Save and Publish each page.


### Listing child pages
Now back in to the template code. Open Layout/LandingPage.ss. Remove the statement "this is the landing page", since it was only a test.

Now its time to loop through and output the child pages of this landing page by adding this code inside the main `<section>` area, below the $Content area.


### Listing child pages
```html
<% loop $Children %>
    <div>
        <h3>$Title</h3>
        <p>$Content.LimitCharacters(50)
            <a href="$Link">Read more...</a>
        </p>
        <hr />
    </div>
<% end_loop %>
```

Save the change to the template file, dev/build, and then look at the Attractions landing page in the front-end of the site. 

You should see the 3 child pages listed with their title, the first 50 characters of the content, and a 'Read more...' link. 



### Listing child pages
If you click the link it should take you to the child page.


### Template conditional and loops

https://docs.silverstripe.org/en/3/developer_guides/templates/syntax/


### Template conditional and loops
As you would have seen from the template code above, SilverStripe allows you to output information from the model or controller class by using variables in the template code. For example $Title, $Content, and $Link output this information from the pages that are children of the landing page.


### Template conditional and loops
Any attribute of a class is available for output in the template, for example if you add an Intro field to the page, where the user can enter a short summary introducing the page, you can output this in the template simply by doing $Intro. We'll do this in the next lesson.


## Loops

Another thing you can do is loops where a section of the template is repeated. 

Loops and conditionals (such as if/else_if/else statements) are denoted with tags which begin and then end with an angle bracket and a percent sign

```
<% loop $Children %>
```

And to end the loop you must do

```
<% end_loop %>
```


### Template conditional and loops
Inside the loop the "scope" is that of the child item, so when you use the variables $Title, $Content, and $Link what is output are the details of the child page, not the landing page.


## Conditionals

Conditionals are defined in the same manner, with tags like this:
 ```
<% operator %> .... <% end_operator %>
```


### Conditionals
For example:

```html
<% if $Intro %>
    <p>$Intro</p>
<% else_if $Content %>
    $Content
<% else %>
   <p>Sorry no information to display</p>
<% end_if %>
```


### Conditionals
As expected you can test if something equals or does not equal a condition. You can use "not" or "!=" for this purpose. Also you can do greater than, less than tests with <, <=, >=, >


### Conditionals
For example

```html
// Equals
<% if $TimePeriod == '7Days' %>
   <p>7 day data</p>
<% end_if %>

// Not equals
<% if not $TimePeriod %>
   <p>Unknown time period specified</p>
<% end_id %>

// Greater than
<% if $Results > 20 %>
   <span>Pagination...</span>
<% end_if %>
```


### Boolean logic

Also as expected, you can do boolean logic, for example requiring that 2 conditions are met before displaying something, or you can do an OR to display something if only 1 condition is set.

The double ampersand && means "and", the double pipe / vertical bar || means "or".


### Boolean logic
For example
```html
// And
<% if $TimePeriod == '7Days' && $Format == 'List %>
    <table>etc..
<% end_if %>

// Or
<% if $ClassName == 'GardenEventPage' || $ClassName == 'EventDetailsPage' %>
   <p>Some content here</p>
<% end_if %>
```


### Many more...

* There is also a lot more you can do in the templates which won't be covered today. 
    + `with / end_with` and `control / end_control` (these are synonyms)
    + `<% include SomeTemplate SomeValue=XXXXX,SomeOtherValue=YYYY %>`


## Further reading

* More info:
    + SilverStripe template syntax https://docs.silverstripe.org/en/4/developer_guides/templates/syntax/
    + Common template variables https://docs.silverstripe.org/en/4/developer_guides/templates/common_variables/
    + Template inheritance https://docs.silverstripe.org/en/4/developer_guides/templates/template_inheritance/


### Next

In the next lesson we will show how to employer getter methods in your template code. We will learn how to add some database fields to our Landing Page, including those inherited from a parent Page class. We will update these database fields using the CMS. We will also learn what a "Getter" method is and how they work.

[Lesson 06 - Introduction to CMS fields](06_IntroToCMSFields.md)