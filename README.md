# SubmitToHubspot
Module for ProcessWire that submits a custom form to a Hubspot CRM Form.

## How To Install
1. Download the [zip file](https://github.com/danielstieber/LeadToHubspot/archive/master.zip) at Github or clone directly the repo into your `site/modules`
2. If you dowloaded the `zip file`, extract it in your `sites/modules` directory
3. Goto the modules admin page, click on refresh and install it.

## Settings
You must set up your Hubspot Portal ID. You can find it in the top right corner of you Hubspot interface. 

![module settings](https://i.imgur.com/qvlNhUJ.png)

## Usage
1. Create a form in **Hubspot Marketing -> Lead Capture -> Forms**
2. Copy the form guid for later use (You can find it in the URL: https://app.hubspot.com/forms-two/{portal_id}/editor-two/{form_guid}/edit/form)
3. Create your custom form with the same fields and a sever-sided validation
4. After form submit, call the module: `$hubspot = $modules->get("SubmitToHubspot");`
5. Call function submitForm and pass your **form guid**, your **data array**, the **page url** (optional), the **page path** (optional), the **hubspot usercookie** (optional) and the user **ip-adress** (optional).

## Important Notes
* Use a sever-sided Validation like [Valitron](https://github.com/vlucas/valitron) before submitting fields
* Make sure, every submitted field is a field in your Hubspot form

## Example Code
Example data array:
```PHP
$data = [
  'email' => 'john.doe@example.com',
  'firstname' => 'John',
  'lastname' => 'Doe',
  'company' => 'ExampleCompany Ltd.',
  'phone' => '123456789'
];
```

Example usage after a form is submitted on your page:
```PHP
// ... validation of form data
$formGuid = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx';
$data = [
  'email' => $input->post->email,
  'firstname' => $input->post->firstname,
  'lastname' => $input->post->lastname,
  'message' => $input->post->message,
  'accept_terms_and_conditions' => strtotime('today midnight') * 1000, // Date fields in Hubspot require a timestamps in miliseconds from midnight
];

if($hubspot->submitForm($formGuid, $data, $page->httpUrl, $page->title, $_COOKIE['hubspotutk'], $_SERVER['REMOTE_ADDR']))
  echo "Thank you for your Message.";
else
  echo "Oops, an error accured while transmitting your data. We are sorry for the inconvinience. For your own security, your data has not been saved. Why not contact us directly at office@companyemail.com and we have talk about your request, while our IT-team is fixing the problem?";
```

## Troubleshooting 
In case of trouble check your ProcessWire warning logs.

### Working with Date & DateTime Data
Hubspot is very picky, when it comes to Date and DateTime fields. (Learn more here: [How should timestamps be formatted for HubSpot's APIs?](https://developers.hubspot.com/docs/faq/how-should-timestamps-be-formatted-for-hubspots-apis))

Example:
```PHP
$data = [
 	'email' => $input->post->email,
 	'newsletter_registration_date' => strtotime('today midnight') * 1000, // in that case, newsletter_registration_date is a Hubspot Datepicker property
]
```
