---
title: "Testing DataAnnotation-based validation in ASP.NET MVC"
author: johan
date: 2009-11-18 22:27:15
updated: 2009-12-11 16:08:05
slug: testing-dataannotation-based-validation-in-asp.net-mvc
tags: [asp.net, asp.net mvc, tdd, dataannotations, validation]
---

*UPDATE: *[*Aaron Jensen*](http://codebetter.com/blogs/aaron.jensen)* pointed out that this only works for .NET 4. The DataAnnotation.Validator class mentioned in the post does not exist in .NET 3.5, so this method does not work.*

With .NET Framework 3.5 SP1 came [DataAnnotations](http://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx), and with [ASP.NET MVC 2](http://aspnet.codeplex.com/wikipage?title=MVC&referringTitle=Home) Preview 1 came [built-in support for DataAnnotation validation](http://weblogs.asp.net/scottgu/archive/2009/07/31/asp-net-mvc-v2-preview-1-released.aspx). This makes basic validation, like required fields, number intervals and so on, very simple. In order to be able to test this validation, though, you have to mimic some of the work that the ASP.NET MVC model binder does. This post will describe both how you get the validation working and how you can test it.

### Decorating the properties

The first thing you have to do, is add the metadata to you data classes. You can either do this in the actual class, or you can add a partial class to hold your metadata. Either way has it’s advantages, here I’ll show the simplest on, just adding the attributes to the class.
  

``` csharp 
using System.ComponentModel.DataAnnotations;

public class User 
{
    [Required(ErrorMessage = "Username is required")]
    [StringLength(25)]
    public string Username { get; set; }

    public string Alias { get; set; }
}
```





All the attributes can be found in *System.ComponentModel.DataAnnotations*.

### Adding validation to the view

Now, we add some validation code to our view, so we can show the error message. Actually, in ASP.NET MVC2 Preview 2 (or later), this code is already present.




``` xml 
<%= Html.ValidationSummary("Create was unsuccessful. Please correct the errors and try again.") %>

<% using (Html.BeginForm()) {%>
    <fieldset>
        <legend>Fields</legend>
        <p>
            <label for="Username">Username:</label>
            <%= Html.TextBox("Username") %>
            <%= Html.ValidationMessage("Username", "*") %>
        </p>
        <p>
            <label for="Alias">Alias:</label>
            <%= Html.TextBox("Alias") %>
            <%= Html.ValidationMessage("Alias", "*") %>
        </p>
        <p>
            <input type="submit" value="Create" />
        </p>
    </fieldset>

<% } %>
```





Basically, what we do is add placeholders for the individual [validationmessages](http://msdn.microsoft.com/en-us/library/dd492106(VS.100).aspx) and a [validationsummary](http://msdn.microsoft.com/en-us/library/system.web.mvc.html.validationextensions.validationsummary(VS.100).aspx). These are already built-in in the [HtmlHelper](http://msdn.microsoft.com/en-us/library/system.web.mvc.htmlhelper_members(VS.100).aspx). 

### Checking the ModelState

Next, you have to make sure that your controller actually validates your model. Since we’re doing this TDD-style, first we’ll write a test for this. 




``` csharp 
[TestMethod]
public void Create_Will_Not_Accept_Empty_Username()
{
    var controller = new UserController();

    var user = new User();
    var result = controller.Create(user);

    Assert.IsFalse(controller.ModelState.IsValid);
    Assert.AreEqual(1, controller.ModelState.Count);
}
```





What we do here is just sending in an empty User into the Create method, and assert that the [ModelState](http://msdn.microsoft.com/en-us/library/system.web.mvc.controller.modelstate(VS.100).aspx) of the controller is invalid, which it should be since the User did not have a Username. We also check that there is exactly one model error, since the empty Alias property should not cause an error.

Now we have to implement the Create method in the UserController to satisfy this test.




``` csharp 
[AcceptVerbs(HttpVerbs.Post)]
public ActionResult Create(User user)
{if (!ModelState.IsValid)
    {
        return View();
    }
    //Create logic goes here
    return RedirectToAction("Index");
}
```





Thanks to our DataAnnotations, validating the User object is really simple. The model binder will check the data annotations, and update the ModelState of the controller. So all we have to do is check if the modelstate is valid, and if not return the create form view again. So now we run our test, and see if things work:

[![U R FAIL!!](/images/johan_driessen_se/WindowsLiveWriter/8deb96c41ba7_1173E/image_thumb.png "U R FAIL!!")](/images/johan_driessen_se/WindowsLiveWriter/8deb96c41ba7_1173E/image_2.png) 

Oops, guess not. Now why is this? Well, as I mentioned in the previous paragraph, the model binder will check the data annotations and update the modelstate. But when we call the Create method from our test, the model binder is never invoked, so no validation takes place. So in order to be sure that our Create method won’t accept any users without names, we need to mimic the behaviour of the model binder. 

In order to to this, we need to create a [ValidationContext](http://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.validationcontext(VS.100).aspx) (found in System.ComponentModel.DataAnnotations) for our model (the User), and user the [DataAnnotations.Validator](http://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.validator(VS.100).aspx) class to validate the model. And finally add the errors to the modelstate of the controller. This somewhat cumbersome syntax turns out like this:




``` csharp 
var validationContext = new ValidationContext(user, null, null);
var validationResults = new List<ValidationResult>();
Validator.TryValidateObject(user, validationContext, validationResults);
foreach (var validationResult in validationResults)
{
    controller.ModelState.AddModelError(validationResult.MemberNames.First(), validationResult.ErrorMessage);
}
```





So, we add this code after we create the user object, but before we call the create method. And then we run the test again.

[![I love green tests!](/images/johan_driessen_se/WindowsLiveWriter/8deb96c41ba7_1173E/image_thumb_1.png "I love green tests!")](/images/johan_driessen_se/WindowsLiveWriter/8deb96c41ba7_1173E/image_4.png) 

Yay, it works! And if try our code in the browser, we get this nice validation message:

[![image](/images/johan_driessen_se/WindowsLiveWriter/8deb96c41ba7_1173E/image_thumb_2.png "image")](/images/johan_driessen_se/WindowsLiveWriter/8deb96c41ba7_1173E/image_6.png) 

Now, in an actual application, of course we won’t keep the model validation code in the test, but rather extract a method to a base class. But this should give you the idea of how it’s done. And if you want to make it even fancier, just add [a few lines in the view](http://dotnetaddict.dotnetdevelopersjournal.com/clientvalidation_mvc2.htm), and you will get Ajax validation as well!


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:90c4dca5-f8a0-4fef-9f52-2d4e7638240d" class="wlWriterEditableSmartContent">Tags: [asp.net](/tags/asp.net), [asp.net mvc](/tags/asp.net%20mvc), [tdd](/tags/tdd), [dataannotations](/tags/dataannotations), [validation](/tags/validation)</div>
