# Working with forms


## Landing Page enquiry form

In this lesson we are going to create a form displayed on the landing page allowing the public to contact us through it. 

This will show you how to create and process forms in code. 


## Landing Page enquiry form

Before we begin, it's worth mentioning that there is the UserForms module with works with all SilverStripe installations, including CWP. This allows users to create and their own forms within the CMS without asking for a developer to create one for them.

However, there will always be a time where a custom form is necessary to do something that userforms cannot. 


### Landing Page enquiry form
Our form will hook into our ManagedEmail module to send an email that we've edited through the CMS.

There are a few steps to get the form set up, these all happen in the LandingPageContoller.


## Allowed Actions

First I need explain $allowed_actions. Recall from our previous lessons that this is a whitelist: only methods defined on the controller will be allowed to run via a URL.


### allowed_actions
Lets do a quick example to show how this works. Add this code to the LandingPageController class...

```php
public function EnquiryForm()
{
   echo "Hello, this is the enquiry form function";
}
```


### The enquiry form
Save, dev build, and then bring up one of your Landing pages in the front end of your site. Then append /EnquiryForm on to the url and press enter.  You should see a message "Action 'EnquiryForm' isn't allowed on class LandingPageController." 

This is expected. We have not allowed access to this method using the allowed actions array. Let's whitelist our method so we can access it from the template


### The enquiry form
As a test, let's make it so only admins can access the page. Add this code below to the LandingPageController (or edit the exiting empty $allowed_actions array)

```php
private static $allowed_actions = [
    'EnquiryForm' => 'ADMIN'
];
```


### The enquiry form
Save, /dev/build, and ensure you are logged out of the CMS. Then reload the LandingPage/EnquiryForm in your browser and see what happens. 

Can you see the "Hello" message? No. Log in to the CMS in another tab and then reload the LandingPage/EnquiryForm tab. You should now see the message. This demonstrates how you can restrict access to functions in front-end pages to only certain type of users.


### The enquiry form
This SilverStripe docs page explains $allowed_actions https://docs.silverstripe.org/en/4/developer_guides/controllers/access_control/ in more detail. The important thing for us is that in order for the Form we want to create to work, we need to allow it as an action, so lets update the $allowed_actions one last time to set the correct access control which is anyone can access the enquiry method


### The enquiry form
Example:
```php
private static $allowed_actions = [
    'EnquiryForm'
];
```


### Form creation in code

Now lets look at creating the form in code, we will do this inside the EnquiryForm function. There are a few parts to form creation, these are as follows...

```php
$form = Form::create(
    $controller,        // the Controller to render this form on
    $name,              // name of the method that returns this form on the controller
    FieldList $fields,  // list of FormField instances
    FieldList $actions, // list of FormAction instances
    RequiredFields::create()           // optional use of RequiredFields object
);
```


### Form creation in code
In practise creating our Enquiry from looks like this, add this to the LandingPageController...

<small class="w-100">
```php
use SilverStripe\Forms\{EmailField, TextareaField, FormAction, RequiredFields, Form, FieldList, TextField};
public function EnquiryForm()
{
    // Create the fields to go on the form.
    $fields = FieldList::create(
        TextField::create('Name', 'Your Name'),
        TextField::create('Company', 'Company or Organisation'),
        EmailField::create('Email', 'Email Address'),
        TextareaField::create('Details', 'Enquiry Details')
    );

    // Create the form actions - i.e. submit buttons.
    $actions = FieldList::create(
        FormAction::create('doEnquiryForm')->setTitle('Send enquiry')
    );

    // Specify the required fields.
    $required = RequiredFields::create([
        'Name',
        'Email'
    ]);

    // Finally put all this together and create the form.
    $form = Form::create(
        $this,
        'EnquiryForm',
        $fields,
        $actions,
        $required
    );

    // Return the form.
    return $form;
}
    ```
</small>

## Adding the form to the Template

Now lets output the form on the LandingPage near the bottom. Notice how to output the form we put the name of the function $EnquiryForm in to the template just like we would for properties of the LandingPage class...

```html
<div class="row">
    <section class="col-md-10 col-md-offset-1">
        <h3>Booking Enquiry</h3>
        $EnquiryForm
    </section>
</div>
```


### Adding the form to the Template
Save, dev build and load one of your landing pages in the front-end of the site. You should now see the form we created. SilverStripe takes care of creating all HTML mark up to display the fields for you. Don't fill the form in yet as there is actually one more step required before things will work completely.

![Enquiry Form](docs/img/11_booking-enquiry-form.png "Enquiry Form")


### Function to process the form

As well as a function which creates the form, we need a function to do something with the information posted on the form. We will be emailing the submission to someone using the module we created earlier. Other users might include saving the details in the database. We have not created this function yet, but did specify a doEnquiryForm function when we defined the action earlier.

Please add this code to the LandingPageController below the EnquiryForm() function...


### Function to process the form
```php
public function doEnquiryForm($data, Form $form)
{
    // Message displayed to the user after the form has been submitted.
    $form->sessionMessage('Thanks, we will be in touch shortly.', 'success');

    return $this->redirectBack();
}
```


### Function to process the form
Save, /dev/build, reload the LandingPage in the front end of the site and fill out the form. After you click submit you should now see the "Thanks" message.


### Processing submitted form data
We should actually do something in the doEnquiryForm function to get the data off the form. We will find a particular ManagedEmail record in our database and utilise its `send` method to fire off an email.


### Processing submitted form data
Before we begin, log into the CMS and visit the ManagedEmails section. Create a new ManagedEmail with "Booking form" as the label, and whatever you want for the subject and message body. Take care that the To and From addresses are syntactically valid emails or this won't work.


### Processing submitted form data
Replace the `$this->redirectBack()` line with this logic:

```php
// The information submitted in the form is in $data. Lets send it to ourselves via email.
$body = '';
foreach($data as $key => $val) {
    $body .= "\n" . $key . " : " . $val;
}

$email = \YourName\ManagedEmails\ManagedEmail::get()->find('Label', 'Booking form');
if($email && $email->ID) {
    //append the enquiry fields to our email
    $email->Body .= $body;
    $email->send();

    return $this->redirectBack();
}

```


### Processing submitted form data
Save, /dev/build?flush= and try the form out. See if you get emails when the form is submitted, it might take a few minutes to come through.


### If emailing does not work
Because you are just working locally emailing may not be configured or emails could be being sent but are being marked as spam because the from address does not match a real website. If this is the case, rather than spending ages trying to set up and configure emailing and trying to get through spam filters on our local machine, lets just output the email details to the screen. These changes will do that...


### Processing submitted form data
Add the following code after the $email->send() line:
```php
// Create new mail object and send the message.
// add these lines after $email->send() and comment out the redirectBack line
echo "<p>" . $subject . "</p>";
echo "<p>" . nl2br($body) . "</p>";

// Redirect back to the landing page the user just came from.
// return $this->redirectBack();
```


### Processing submitted form data
Alternatively, you can configure SilverStripe's SwiftMailer class to send emails through a properly configured SMTP server or using an API mailing service, such as MailGun or MailChimp. For example...


### Using an email provider
Here is an example using MailGun, which uses Injector to read environment variables and pass them into a new instance of `Swift_SmtpTransport`
<small class="w-100">
```
---
Name: myemailconfig
After:
  - '#emailconfig'
---
SilverStripe\Core\Injector\Injector:
  Swift_Transport:
    class: Swift_SmtpTransport
    properties:
      Host: '`MAILGUN_SMTP_HOSTNAME`'
      Port: 587
      Encryption: tls
    calls:
      Username: [ setUsername, ['`MAILGUN_SMTP_USERNAME`'] ]
      Password: [ setPassword, ['`MAILGUN_SMTP_PASSWORD`'] ]
SilverStripe\Control\Email\Email:
  send_all_emails_from: 'do-not-reply@mail.example.com'
```
</small>

### Further reading/references

* Controller Allowed Actions https://docs.silverstripe.org/en/4/developer_guides/controllers/access_control/
* SilverStripe forms https://docs.silverstripe.org/en/4/developer_guides/forms/
* SilverStripe emails https://docs.silverstripe.org/en/4/developer_guides/email/


## Any last questions?
Thank you for your time!  Alison has some feedback forms for you to fill out. Feedback helps us improve the slides and also helps the trainers learn.  Please leave them on your desk before you go, or with reception on your way out.

The slides will be published online soon, and emailed to you when they are ready!