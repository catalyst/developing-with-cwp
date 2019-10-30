# Working with Themes


### Working with Themes
* The next step in SilverStripe development is choosing which theme you will base the website off
* A good way to get started is to take a copy of one to the "out of the box" CWP themes and then modify it as needed
* Alternatively you can find a theme on the SilverStripe add ons site http://addons.silverstripe.org


### Working with Themes
* Out of the box, with the standard SilverStripe CWP install you only get a "Starter" theme which is intended for agencies creating websites for larger organisations with a strong brand etc where lots of customisations will happen
* Another option is the Wātea theme - this is an enhancement to the Starter theme and is more fully featured, but more tricky to alter since you have to understand where to place the new files and make changes you want.


### Wātea
![Wātea](docs/img/04_watea_screenshot.jpg "Wātea")


### Wātea
* Wātea is a "subtheme" for CWP built on top of the starter theme. To install it, you need to add two more modules to your CWP project:

<small class="w-100">
```
    composer require cwp/watea-theme ^3
    composer require cwp/agency-extensions ^2
```
</small>


### Wātea
* `cwp/agency-extensions` is technically optional, but you'll have a poor experience without it. You'll also need to add Wātea to your `app/_config/theme.yml` file, directly above starter but below `$public`:

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


### What's happening here
* Wātea is a prime example of a _cascading theme_
* When SilverStripe receives a web request and renders it, will first look to the `$public` folder for available templates, icons, fonts, JS, CSS or other web assets
* If it does not find what it needs there, it will "cascade" down to the next available theme, which is watea


### What's happening here
* Watea contains an "enhanced" set of templates for navigation, header, search, and other elements
* Some of these are "include" templates which are not found in the starter theme, but are referenced in the Watea version of your templates
*  Some page templates are only found in starter - the request then cascades down to the "starter" theme


### What's happening here
* "starter" is where most of your base layer templates will exist
* SilverStripe should never be missing a template for a frontend user request at this point, with one notable exception...


### $default
* "$default" contains framework level templates, which is responsible for rendering content such as developer errors, stack traces, and barebones text from a controller
* It also handles the content under the /dev URL
* The most famous example that users will directly experience is the /Security/login screen.


### More about Wātea
* Run `/dev/build?flush=1` again, and note the visual changes to your website.

* Agency Extensions adds a few new features to the CMS that Wātea will automatically use, if it is installed. These include:
    + A Carousel with Hero Image capability added to some pages, or all of them
    + A theme/colour picker
    + custom logos and icon imagery in the header and footer


### More about Wātea
* If you want the ability to customise the colours of Wātea, you'll also need this line to `app/_config/config.yml`:

```yml
SilverStripe\SiteConfig\SiteConfig:
  enable_theme_color_picker: true
```


### More about Wātea
* Other options for the colour picker can be found [here](https://github.com/silverstripe/cwp-agencyextensions/blob/2.1.2/docs/en/01_Features/ThemeColors.md)
* https://github.com/silverstripe/cwp-agencyextensions/blob/2.1.2/docs/en/01_Features/ThemeColors.md


### Advantages of Wātea
* You end up with a highly customiseable theme, aesthetically pleasing, and fully accessible theme built on top of CWP components.
* Very few websites ever require no customisation at all. Wātea offers a diverse set of features; you may be happy with the out of the box experience with little to no development experience required.


### Disadvantages of  Wātea
* Older versions of Wātea are based on Bootstrap 3, but this has changed recently. 
* Ongoing support is difficult, because you can no longer automatically manage the project with composer once you have made changes to it.
* As I said, it is very rare for an agency to *never* make a single change to their theme.
* If you want to build off of Watea, consider the following options...


### Workarounds to work with Wātea :: Option 1
* Create a third theme, which cascades down to Wātea and then Starter.
* Your theme replaces any component found in Wātea, and you become responsible for building your own Javascript and CSS.

* The best way to handle this is to clone the watea folder in the themes directory, then rename the theme to something else...


### Workarounds to work with Wātea :: Option 1 (continued)

* Delete the `.git` folder in your new copy. Re-initialise with `git init`. Add the new theme to `composer.json` in the parent project. Run `composer update` from the base directory
* This creates a version of Wātea that you manage yourself, but you need to take care that nothing in this folder conflicts with behaviour in the previous two modules. 


### Workarounds to work with Wātea :: Option 2
* Delete the `.git` folder from starter and watea
* Add the entire contents of these folders to the version control in the parent project
* You can now make your own changes to your website themes without concern for what the upstream branch is doing


### Workarounds to work with Wātea :: Option 2 (continued)
* This may not be desireable for the same reason - you can no longer depend on updates from the upstream branch. Theme and feature updates are now your responsibility

* You can always choose a different theme, or create your own.


## Creating our own theme

If Watea is a bit much, or doesn't satisfy your requirements, you can create your own theme without too much hassle. The simplest themes have a folder structure that looks something like this...


### Creating our own theme
<small class="w-100">
```
themes
    yourtheme
        dist
            css
                app.css
            js
                app.js
            img
                mylogo.png

        templates
            Includes
                ...
                Navigation.ss
                Footer.ss
            Layout
                ...
                Page.ss
            ...
            Page.ss
      
    ```
</small>


### Creating our own theme
* The top-level Page contains the "global" layout of any request made to the site
* When a user visits a Page, or anything that descends from a Page, they'll see this global layout
* Usually this page contains site-wide variables like $SiteConfig and $CurrentUser


### Creating our own theme
* This is usually where you will place Header and Footer content. Most importantly, will include the $Layout variable, which generally contains Content and things related to a specific page record. 

* `yourtheme` might also contain front-end assets that you've generated using a build tool like gulp or webpack


### Creating our own theme
* These tools manage things like js and scss source files, as well as the compiled output into a `dist` directory, which is then exposed to the `public` portion of the website
* We will cover this `dist` mechanism briefly, but build topics are a very complex topic outside the scope of this course.


## Installing your theme
* There's generally two ways to do this:
    * commit all changes to version control. This will also include changes to your codebase and means that the theme is considered to be part of your project.
    * manage your theme seperately as its own git repository. The parent project will pull in changes to your theme from composer. This means your theme can have a "stable" tagged release version. It can also be dropped easily into other SilverStripe installations.


### Installing your theme
* Either approach is valid. For the sake of this training course, I will include the theme as part of the parent repository.


### Installing your theme
1. Replace "starter" and "watea" with "yourtheme" (or whatever you've named your folder) in `app/_config/theme.yml`
2. Run a dev/build?flush=1 to view your changes


## Getting started

Let's create an example from the Bootstrap 4 starter template:



### Starter template in Page.ss
<small class="w-100">
```html
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

    <title>$Title :: $SiteConfig.Title</title>
  </head>
  <body>
    <header>
        <% include Navigation %>
    </header>
    $Layout

    <footer>
        <% include Footer %>
    </footer>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
  </body>
</html>
```
</small>


### Navigation and Footer
I have also included two templates for Navigation and Footer, which separates those concerns into their own templates. 


### Navigation
<small class="w-100">
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="$BaseHref">$SiteConfig.Title</a>
  <ul class="navbar-nav ml-auto">
      <% if $CurrentUser %>
      <li class="nav-item dropdown">
        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          Welcome $CurrentUser.Email
        </a>
        <div class="dropdown-menu" aria-labelledby="navbarDropdown">
            <a class="dropdown-item" href="/Security/logout?SecurityID=$SecurityID">Logout</a>
        </div>
      </li>
      <% else %>
      <li class="nav-item">
        <a class="nav-link" href="/Security/login" title="Login">
        Login
        </a>
      </li>
      <% end_if %>
  </ul>
</nav>
```
</small>


### Footer
<small class="w-100">
```html
<ul class="nav justify-content-end">
    <li class="mr-auto">
        <a href="$BaseHref">
            <img src='$ThemeDir("themes/yourtheme")/dist/img/logo.png' alt="Logo" style="width:32px;height:32px" class="mt-3"/>
        </a>
    </li>
    <% loop Menu(1) %>
    <li class="nav-item m-4">
        <a class="p-4" href="$Link" title="$MenuTitle">$MenuTitle</a>
    </li>
    <% end_loop %>
</ul>
```
</small>


### Layout/Page.ss
<small class="w-100">
```html
#Layout/Page.ss
$Content
$Form
```
</small>


### Your own theme
* As you can see, the special `$Layout` variable is populated with $Content from the Layout/Page.ss
* $Form is used to inject the Login form, which is a special type of Page
* You can also see the template includes Navigation and Footer being automatically filled in


### Your own theme: something's broken
We still need to expose our new assets in the public directory. Add this to your composer.json's `extra` key in the parent project
<small class="w-100">
```
    "extra": {
        "expose": [
            "themes/yourtheme/dist"
        ],
        ...
```
</small>
In the base directory, run `composer vendor-expose`


### Your own theme
* Template includes are very powerful, as they allow you to re-use common elements of your site
* They can maintain their own set of variables, conditional blocks, loops, and other logic
* You can also pass variables into these templates as parameters


### Your own theme
* Templates can also invoke methods on their controllers known as "Getters", which are functions which retrieve data from any source and display them on the page
* Getters are also used to fetch database information, or information from relations
* We'll cover this more when we discuss creating new page types


## Namespaces
* You may have noticed that our "Page" template corresponds with our "Page" class, while Layout/Page relates to content on the Page,  which is an awesome convention. But what if your class is namespaced?

* SilverStripe 4 introduced namespaced classes, which required some fairly significant changes to existing themes. Themes now utilise a folder structure to mimic namespaces.


### Namespaces and templates
* Templates defined on your theme can replace those defined in a module
* For example, if you wanted to overload the Blog template with your own design, you can do this by copying the template (including the folder structure) to the templates folder of your theme:


### Namespaces and templates
```
themes
    yourtheme
        templates
            SilverStripe
                Blog
                    Model
                        Layout
                            BlogPost.ss

```


### Namespaces and templates
* Any changes you make to your themed version of the template will take precedence over templates defined on the module
* However, you must ensure that the folder structure matches that of the module exactly
* This can be confusing to new developers, but once this convention is learned it can be a powerful tool
* One common use-case for this is redefining the EditableFormField templates for the userforms module to include Bootstrap styling.


### Namespaces and templates
* One exception to this is overloading the Security_* templates, which continue to work without namespacing
* You would typically overload these forms if you wanted to make some changes to the login, logout, change password, or password reset screens. Those templates are:


### Namespaces and templates
```
themes
    yourtheme
        templates
            Security_login.ss
            Security_logout.ss
            Security_changepassword.ss
            Security_resetpassword.ss
```
* Consider using `silverstripe/login-forms` instead. Future SilverStripe versions will include this by default


## Further reading

* SilverStripe add ons http://addons.silverstripe.org
* Working with the starter theme https://www.cwp.govt.nz/developer-docs/en/2/working_with_projects/customising_the_starter_theme


## Conclusion
* In the next lesson, we'll learn how to create a new page type, defining fields which can be retrieved from the database automatically using SilverStripe's ORM tool, and how to create a template for this new page using the conventions described above

* [Lesson 04 - Creating a new Page Type](04_CreatingANewPage.md)