---
title: "Updated Anti-XSRF Validation for ASP.NET MVC 4 RC"
extid: posts/9377
author: johan
date: 2012-06-05 10:42:15
updated: 2012-06-05 10:43:24
slug: Updated-Anti-XSRF-Validation-for-ASP.NET-MVC-4-RC
tags: [asp.net mvc, javascript, validation, xsrf, security]
---

 In our project, we’ve been using [Phil Haack’s method for preventing cross-site request forgeries for JSON posts](http://haacked.com/archive/2011/10/10/preventing-csrf-with-ajax.aspx) by inserting the request verification token as a header in the request, and then using a custom `ValidateJsonAntiForgeryToken` attribute to validate it. And it’s been working just fine.

 However, with the recent release of [ASP.NET 4 MVC RC](http://www.asp.net/mvc/mvc4), it didn’t work anymore. To my initial dismay, it didn’t even compile anymore. Turns out that the method, `AntiForgery.Validate(HttpContextBase httpContext, string salt)` , that hade been used to validate the tokens is now obsolete.

 However, this turned out to be a good thing, as [the MVC developers have made it easier to configure the anti-XSRF validation](http://aspnetwebstack.codeplex.com/SourceControl/network/forks/jontsao/aspnet/changeset/changes/de43b6ad7568). You can now provide the tokens directly to the Validate method, and thus there is no need to create a wrapper for the HttpContext and the HttpRequest anymore.

 Instead, you can just call the validate method with the proper tokens directly from your attribute:



``` csharp 

[AttributeUsage(AttributeTargets.Method | AttributeTargets.Class, 
                AllowMultiple = false, Inherited = true)]
public sealed class ValidateJsonAntiForgeryTokenAttribute 
                            : FilterAttribute, IAuthorizationFilter
{
    public void OnAuthorization(AuthorizationContext filterContext)
    {
        if (filterContext == null)
        {
            throw new ArgumentNullException("filterContext");
        }

        var httpContext = filterContext.HttpContext;
        var cookie = httpContext.Request.Cookies[AntiForgeryConfig.CookieName];
        AntiForgery.Validate(cookie != null ? cookie.Value : null, 
                             httpContext.Request.Headers["__RequestVerificationToken"]);
    }
}

```



 And, just to make this post complete, in case the original post is removed, this is the javascript required to add the header to the request:



``` js 

postJsonWithVerificationToken: function (options) {
    var token = $('input[name=""__RequestVerificationToken""]').val();
    var headers = {};
    headers['__RequestVerificationToken'] = token;

    $.ajax({
        cache: false,
        dataType: 'json',
        type: 'POST',
        headers: headers,
        data: options.jsonData,
        contentType: 'application/json; charset=utf-8',
        url: options.url,
        success: options.onSuccess,
        error: options.onError
    });
}

```



 Finally, you just use it as you would the standard `ValidateAntiForgeryToken` attribute, by decorating your action method like this:



``` csharp 

[HttpPost, ValidateJsonAntiForgeryToken]
public ActionResult DoSomething(Foo foo) {
    //Do something
}

```



 And in your form, you just call `@Html.AntiForgeryToken()`, just as you would for a normal form post.

 A lot cleaner than the previous haack (although it was very clever)!

<div class="wlWriterSmartContent" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:7e1eaf7d-1aa2-4732-9c7d-a0e48d1dfc10" style="margin: 0px; padding: 0px; float: none; display: inline;">
	Tags: [asp.net mvc](/tags/asp.net+mvc), [javascript](/tags/javascript), [validation](/tags/validation), [xsrf](/tags/xsrf), [security](/tags/security)</div>
