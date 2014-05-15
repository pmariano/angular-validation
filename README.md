angular-validation 1.1.2
=========================
[![NPM version](https://badge.fury.io/js/angular-validation.svg)](http://badge.fury.io/js/angular-validation)
[![Build Status](https://travis-ci.org/huei90/angular-validation.png?branch=master)](https://travis-ci.org/huei90/angular-validation)
[![devDependency Status](https://david-dm.org/huei90/angular-validation/dev-status.png)](https://david-dm.org/huei90/angular-validation#info=devDependencies)
[![Gitter chat](https://badges.gitter.im/huei90/angular-validation.png)](https://gitter.im/huei90/angular-validation)

Requirement
-----
[AngularJS](http://angularjs.org) > 1.2.0, 1.3.0-beta

DEMO
-----
http://huei90.github.io/angular-validation/

http://plnkr.co/edit/Nwk9cT (Bootstrap framework)

License
-----
MIT

Install
-----
Install with npm

```
npm install angular-validation
```

Using angular-validation
---
```javascript
angular.module('yourApp', ['validation']);
```

Writing your Code
====
**Add Valid Message (error, success) for validation `required`** <br/>
`required-error-message` and `required-success-message`

```html
<label>Required</label>
<input type="text"
       name="require"
       ng-model="form.required"
       validator="required"
       required-error-message="Required"
       required-success-message="Good Required"/>
       <!-- or you can try
       required-error-message="{{ RequiredMsg }}"
       -->
```

**Add Valid Message (error, success) for validation `email`** <br/>
`email-error-message` and `email-success-message`

```html
<label>Email</label>
<input type="text"
       name="email"
       ng-model="form.email"
       validator="email"
       email-error-message="Error Email"
       email-success-message="Good Email"/>
```

**Use Default Valid Message** <br/>
*you don't need to give valid message*

```html
<label>Number</label>
<input type="text" name="number" ng-model="form.number" validator="number"/>
```

<a name="no-validation-message"></a>
**Don't show the Valid Message `no-validation-message="true"`**

```html
<label>Number</label>
<input type="text" name="number" ng-model="form.number" validator="number" no-validation-message="true"/>
<!-- or {{ your.Scope.Name }} -->
<input type="text" name="number" ng-model="form.number" validator="number" no-validation-message="{{ noValidationMessage }}"/>
```

**Add Valid Callback Function, `invalid-callback` & `valid-callback`**

```html
<label>Required (Invalid Callback alert)</label>
<input type="text" name="requiredCallback" ng-model="form.requiredCallback" validator="required" invalid-callback='error("Must be Required");'/>
```

**Select the validation method `watch` `blur` `submit` `submit-only`, default as `watch`** <br/>
`validationProvider.validate(form).success(callback).error(callback)` use callback to continue your submit

```html
<label>Watch method</label>
<input type="text" name="number" ng-model="form.number" validator="number" valid-method="watch"/>
<input type="text" name="number" ng-model="form.number" validator="number"/>

<label>Blur method</label>
<input type="text" name="number" ng-model="form.number" validator="number" valid-method="blur"/>

<label>Submit method</label>
<form name="Form">
    <input type="text" name="number" ng-model="form.number" validator="number" valid-method="submit"/>
    <button ng-click="form.submit(Form)"></button>
</form>

<label>Submit Only method</label>
<form name="Form">
    <input type="text" name="number" ng-model="form.number" validator="number" valid-method="submit-only"/>
    <button ng-click="form.submit(Form)"></button>
</form>

<script>
    // ... validate method, it will check `checkValid(Form)`
    $scope.form = {
        submit: function (form) {
            $validationProvider.validate(form)
                .success(successCallback)
                .error(errorCallback);
        }
    };
    // ...
</script>
```

**Setup a new Validation `setExpression()` `setDefaultMsg()` with `RegExp` or `Function` in config phase**
<a name="custom-function-huei"></a>

```html
<!-- View -->
<label>IP address (Custom setup the new validator - RegExp)</label>
<input type="text" name="ip" ng-model="form.ip" validator="ip"/>

<label>Huei (Custom setup the new validator - Function)</label>
<input type="text" name="huei" ng-model="form.huei" validator="huei"/>
```

```javascript
// your module
angular.module('yourApp', ['validation'])
    .config(['$validationProvider', function ($validationProvider) {
        // Setup `ip` validation
        var expression = {
            ip: /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/
        };

        var validMsg = {
            ip: {
                error: 'This isn\'t ip address',
                success: 'It\'s ip'
            }
        };

        validationProvider.setExpression(expression); // set expression
        validationProvider.setDefaultMsg(validMsg); // set valid message

        // Setup `huei` validation
        validationProvider.setExpression({

            /**
            * @param value , user input
            * @returns {boolean} true iff valid
            */
            huei: function (value) {
                return value === 'Huei Tan';
                // or you can do
                return $q.all([obj]).then(function () {
                // ...
                    return true;
                })
            }
        });

        validationProvider.setDefaultMsg({
            huei: {
                error: 'This should be Huei Tan',
                success: 'Thanks!'
            }
        });
    }]);
```

**Check form whether valid, return `true` if valid. `checkValid()`** <br/>
**Reset the specific Form. `reset()`**

```html
<form name="Form">
    ...
    <!-- Check the entire form valid from angular-validation `valid` -->
    <button ng-disabled="form.checkValid()"></button>
    <!-- or Check the specific form(Form) valid from angular `$valid` -->
    <button ng-disabled="form.checkValid(Form)"></button>
    <!-- Reset the specific Form -->
    <button ng-click="form.reset(Form)"></button>
</form>
```

```javascript
    // ... checkValid
    $scope.form.checkValid = validationProvider.checkValid;
    // ... reset
    $scope.form.reset = function (form) {
        validationProvider.reset(form);
    };
```

**Set the valid/invalid message style CSS**

```html
<span><p class="validation-valid">Your valid message here<p></span>
<span><p class="validation-invalid">Your invalid message here<p></span>
```

```css
.validation-valid {
    <!-- valid style -->
}

.validation-invalid {
    <!-- invalid style -->
}
```

**Custom the valid/invalid message style HTML in `.config()`,** <br/>
`setErrorHTML(func)` `setSuccessHTML(func)`, input should be a `function` and given `parameter` which is the valid/invalid message declared
in `getDefaultMsg()`,and finally return the HTML code

```javascript
// your angular
.config(['$validationProvider', function ($validationProvider) {
    $validationProvider.setErrorHTML(function (msg) {
        // remember to return your HTML
        // eg: return '<p class="invalid">' + msg + '</p>';
    });

    $validationProvider.setSuccessHTML(function (msg) {
        // eg: return '<p class="valid">' + msg + '</p>';
    });
}]);
```

**disable/enable show success/error message**<br/>
`default: true`<br/>
Easily disable success/error message

```javascript
// your angular
.config(['$validationProvider', function ($validationProvider) {
    $validationProvider.showSuccessMessage = false; // or true(default)
    $validationProvider.showErrorMessage = false; // or true(default)
}]);
```

Built-in validation <small>in angular-validation</small>
===

1. Required
2. Url
3. Email
4. Number

Anyone can give a `PR` for this angular-validation for more `built-in validation`

Developer <small>Adding a new validation</small>
=====
**Clone the repo to your computer**
```
git clone https://github.com/huei90/angular-validation.git
```

**Install Grunt and Download dependencies**
```
npm install -g grunt-cli
npm install
```

**Before coding** <small>Boot the  Environment</small>
```
grunt dev
```

**Start coding**
```
open `provider.js`, looking for `var expression` and `var defaultMsg`
Adding a new expression and defaultMsg to extend it
```

**IP validation** <small>As the example</small>
```javascript
// provider.js
var expression = {
    required: /.+/gi,
    ... // add new expression below
    ip: /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/
};

var defaultMsg = {
    required: {
        error: 'This should be Required!!',
        success: 'It\'s Required'
    },
    ... // add new valid message below
    ip: {
        error: 'This isn\'t ip',
        success: 'It\'s IP'
    }
};
```
**Test**
```
Karma Test done by Travis-ci,
When you are done, test it on `http://localhost:8080`
```

**Give me a PR** <small> Thanks for it </small>

**Who does the exactly same job ?**

1. https://github.com/kelp404/angular-validator
2. https://github.com/nelsonomuto/angular-ui-form-validation
3. (Waiting list..)


**Note**

1. More Status 
       https://github.com/angular/angular.js/issues/583#issuecomment-31277556
2. ngForm module ideas 
       https://docs.google.com/document/d/192dCUnoIBQ7-vurvPeS9BElGdxfk0Ddcof2KzDDi1Mc/edit

CHANGELOG
=====
see [release](https://github.com/huei90/angular-validation/releases)

Q & A
=====
###Can I validate the form when init ? [#10](https://github.com/huei90/angular-validation/issues/10)###

```html
<form name="Form">
    <input type="text" name="number" ng-model="form.number" validator="number"/>
    <button id="button1111" ng-click="form.submit(Form)">Show</button>
</form>
```
```javascript
$timeout(function () { // call $timeout to make sure the Form Constructor is generated
$validationProvider.validate($scope.Form); // $scope.Form is html form name `Form Constructor`
});
```

###What's the differentiate between validator-method `submit` and `submit-only`[#4](https://github.com/huei90/angular-validation/issues/4)###

`submit` : when user click submit, then start watching using `watch` to validate<br/>
`submit-only` : when user click `submit`, doesn't validate through `watch` until `submit` button is clicked.