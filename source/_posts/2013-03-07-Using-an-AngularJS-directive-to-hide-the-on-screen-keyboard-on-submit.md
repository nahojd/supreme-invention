---
title: "Using an AngularJS directive to hide the on screen keyboard on submit"
extid: posts/9505
author: johan
date: 2013-03-07 20:50:11
updated: 2013-03-07 20:50:11
slug: Using-an-AngularJS-directive-to-hide-the-on-screen-keyboard-on-submit
tags: [angularjs, responsive design, html, mobile, javascript]
---

Recently, I’ve been working on a responsive web site running [AngularJS](http://angularjs.org/).  In this, we have a search form. As search forms usually works, you enter a query in a text field, and then you click the button to search. Or, more likely, you hit enter. Now, since this is a responsive site, this needs to work on a phone too. This is were it gets a bit more tricky.

[![A search form](/images/Windows-Live-Writer/1b770bd388b0_71DE/image_thumb.png "A search form")](/images/Windows-Live-Writer/1b770bd388b0_71DE/image_2.png)

When you click the text field, your on screen keyboard pops up. You enter your query, and click Enter, or Next, or Go or whatever the button is called on your keyboard. This submits the form, but unfortunately, the soft keyboard still lingers. If you had clicked the button instead, your keyboard would have disappeared. Why? Because when you click enter on your on screen keyboard, your text field still has focus!

So in order to rectify this, we need to make sure that the text field loses focus when the enter/go/next-button is clicked. It’s easy to make an object lose focus, you just call `blur()` on it. The problem is when to call it. What happens when you press the enter key on your on screen keyboard? Well, that depends.

Take this form for example:

``` html
<form>
  <input type="text" name="query" />
  <button>OK</button>
</form>

```

Now, in this case, when I press enter in the text field, two events will fire: the submit event on the form, and the click event on the button. The exact same thing happens when you click the button Why? Because [the default behaviour of a button in HTML5 is to submit the form](http://dev.w3.org/html5/markup/button.html). In order to prevent this, you need to use `<button type="button">OK</button>` instead.

If you do this, pressing enter in the text field will still submit the form, but not click the button. And vice versa, clicking the button will fire the click event on the button, but not the submit event on the form!

Since this is an AngularJS application, the easiest way is to specify a submit action for the form, and then make sure the form submits whether you click the button or press enter. Like this:

``` html
<form ng-submit="foo()">
  <input type="text" ng-model="query" name="query" />
  <button type="submit">OK</button>
</form>
```

A nice and pretty form, but this still leaves me with the original problem, who do I make sure that the text field loses focus whenever I click enter/ok/go on my soft keyboard, or to put it in more technical terms, how do I run blur() on the text field whenever the submit event fires? This is still an AngularJS app, so the answer is of course – a directive! I came up with this:

``` js
angular.module('Foobar', [])
.directive('handlePhoneSubmit', function () {
    return function (scope, element, attr) {
        var textFields = $(element).children('input[type=text]');

        $(element).submit(function() {
            console.log('form was submitted');
            textFields.blur();
        });
    };
});

```

Then, you just apply this to the form: `<form handle-phone-submit>`, and like magic, your soft keyboard disappears whether you press enter/ok/go or the button. There’s a [jsfiddle](http://jsfiddle.net/ns4ER/4/) with a working example.

I allowed myself to use jQuery within the directive, but naturally you could do this with just the jQuery lite implementation that always exist in Angular. In that case, the directive function could look something like this:

``` js
return function (scope, element, attr) {
    var textFields = element.find('input');

    element.bind('submit', function() {
        console.log('form was submitted');
        textFields[0].blur();
    });
};

```

There are some limitations there, but it seems to work ok.