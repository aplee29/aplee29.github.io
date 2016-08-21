---
layout: post
title:  "What was interesting about Forms and Validation?"
date:   2016-08-21 21:44:18 +0000
---

On the client side, AngularJS provides built-in methods for form validation. Previously, users were notified about validation errors *after* form submission. AngularJS, on the other hand, provides instant feedback for users *before* the form is even sent across the Internet.

One of the ways the Angular framework validates a form is by managing its current state. What this means is that it keeps track of the form and its input fields, relaying information on whether or not the user has previously entered data into a field box, modified the data, etc. 

The following **states** represent the status of a form/input fields (all of these properties contain the boolean values `true` or `false`):

* `$untouched` - The user has *not* touched the field. Its initial value is set to `true`, and changes to `false` after the user clicks outside of the field box.
* `$touched` - The user has already touched the field.
* `$pristine` - The user has *not* modified the field. Its initial value is set to `true`, and changes to `false` after the user changes the field's value.
* `$dirty` - The user has already modified the field.
* `$valid` - The field value is valid.
* `$invalid` - The field value is *not* valid.
* `$error` - An object of all the input's failures. 

Here is a simple example showing a typical form utilizing Angular validation.

```html
<form name="myForm">
   <input name="myInput" ng-model="myInput" required="required">
</form>
```

By default, the **state** properties are set to the following:

* `$untouched: true`
* `$touched: false`
* `$pristine: true`
* `$dirty: false`
* `$valid: false`
* `$invalid: true`
* `$error: { required: true }`

As it stands now, our form is invalid because the required input field is empty. 

Say that the user fails to fill in the field and attempts to submit the form. Let's display an error message to the user:

```html
<form name="myForm">
   <input name="myInput" ng-model="myInput" required="required">
	 <span ng-show="myForm.myInput.$touched && myForm.myInput.$invalid">This field is required.</span>
</form>
```

The `ng-show` directive renders the helpful error message ("This field is required.") if the input field has been touched (`$touched`) and is left blank (`$invalid`). Pretty awesome!

Although Angular's form validation offers a more optimal user experience, it should also be noted that client-side validation alone does not secure users' data. It's important to keep in mind that all secure web applications must also validate their data input on the server end.
