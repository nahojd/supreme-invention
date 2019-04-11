---
title: "Using dynamic objects in ASP.NET MVC and RavenDB"
extid: posts/7169
author: johan
date: 2011-08-18 01:05:51
updated: 2011-08-18 01:05:51
slug: Using-dynamic-objects-in-ASP.NET-MVC-and-RavenDB
tags: []
draft: true
---

In my blog engine, Alpha, there’s still lots of hard-coded stuff, that should really be configurable from the admin pages. No big deal, since I guess I’m the only one using this fabulous blog engine, but I think the time has come to fix it. 

Now, since I’m using RavenDB, I will naturally be saving a Config document to the database, and that means I pretty much just have to create a Config class with properties for all the stuff I want to be able to configure, and create an admin page for that object. Easy peasy. But I was thinking, do I really have to create a Config class? Couldn’t I just use a dynamic object? Turns out that, yes, I could. 

I haven’t implemented this in Alpha yet, or even decided if I should (I’m leaning towards not…), but I thought it might be interesting to see how it could be done anyway. 

Ok, let’s take the most optimistic approach and just pretend everything will work. So, let’s make a simple config controller.
<div style="margin: 0px; padding: 0px; float: none; display: inline;" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:2f0c2d90-364d-4d0c-abd2-61df6ab5f066" class="wlWriterEditableSmartContent">

public class ConfigController : Controller
    {
        private readonly IDocumentSession session;
        //We’re assuming that the IDocumentSession will be injected somehow (I’m using StructureMap, myself). 
        public ConfigController( IDocumentSession session )
        {
            this.session = session;
        }

        public ActionResult Edit()
        {
            dynamic config = session.Load<ExpandoObject>( "config/1" );
            ViewBag.Config = config ?? new ExpandoObject();
            return View();
        }

        [HttpPost]
        public ActionResult Config( FormCollection form )
        {
            dynamic config = MagicallyCreateDynamicFromFormCollection( form );
            config.Id = "config/1";
            session.Store( config );
            session.SaveChanges();
            return RedirectToAction( "Edit" );
        }
    }
```


</div>

Ok, so this is pretty straight forward. We load the Config as an ExpandoObject from the database, or create a new if it does not exist (like the first time). I use a hard coded id, since there will only be one Config document in the database anyway. Then I just stick it in the ViewBag – no point using strongly typed views if we’re gonna use dynamics anyway!

When we save the config it gets a little trickier – we somehow have to create a ExpandoObject from the values in our form, which we get as a FormCollection. And no, we can’t just use ActionResult Config( ExpandoObject config), we’ll just get an empty object. So we need to create an ExpandoObject from the FormCollection.  Except from that, we just set the Id and save the document. 

So, how about the View? 
<div style="margin: 0px; padding: 0px; float: none; display: inline;" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:5701ff94-f331-4beb-b352-48cce7fa45ed" class="wlWriterEditableSmartContent">

``` html 
<h2>Config</h2>

@using (Html.BeginForm())
{
	<label>Blog name</label>
	<input type="text" value="@ViewBag.Config.BlogName" id="BlogName" name="BlogName" />

	<label>Author name</label>
	<input type="text" value="@ViewBag.Config.Author" id="Author" name="Author" />

	<input type="submit" value="Save" />
}
```

</div>
