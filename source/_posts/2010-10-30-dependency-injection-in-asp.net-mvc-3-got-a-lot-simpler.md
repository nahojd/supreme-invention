---
title: "Dependency injection in ASP.NET MVC 3 got a lot simpler"
author: johan
date: 2010-10-30 19:49:50
updated: 2010-10-30 19:49:50
slug: dependency-injection-in-asp.net-mvc-3-got-a-lot-simpler
tags: [asp.net mvc, dependency injection, structuremap]
---

In my project of writing my own blog engine, I’m using [ASP.NET MVC 3 beta](http://www.asp.net/mvc/mvc3), and I like what they have done to [simplify the dependency injection](http://bradwilson.typepad.com/blog/2010/07/service-location-pt1-introduction.html)! Let me show you the difference.

### The MVC 2 way

First, I need a registry (that’s just [ScructureMap](http://structuremap.net), nothing really to do with MVC).
  

``` csharp 
//The most simple registry I could think of
public class AlphaRegistry : Registry
{
    public AlphaRegistry()
    {
        Scan( x =>
        {
            x.AssembliesFromApplicationBaseDirectory();
            x.WithDefaultConventions();
        } );
    }
}
```





Then, in MVC 2, you needed to make your own `ControllerFactory` (actually, you could get this from the [MvcContrib project](http://mvccontrib.codeplex.com/), if it was up to date):




``` csharp 
public class StructureMapControllerFactory : DefaultControllerFactory
{
    protected override IController GetControllerInstance( RequestContext requestContext, Type controllerType )
    {
        if (controllerType == null)
            return base.GetControllerInstance( requestContext, controllerType );

        IController result = null;
        try
        {
            result = ObjectFactory.GetInstance( controllerType ) as Controller;
        }
        catch (StructureMapException)
        {
            System.Diagnostics.Debug.WriteLine( ObjectFactory.WhatDoIHave() );
            throw;
        }
        return result;
    }
}
```





And finally, you needed to replace the default `ControllerFactory` with the `StructureMapControllerFactory`:




``` csharp 
public class MvcApplication : HttpApplication
{
    protected void Application_Start()
    {
        //Other stuff that need to be done on application start here

        ControllerBuilder.Current.SetControllerFactory( typeof( StructureMapControllerFactory ) );
    }
}
```





Not the most difficult thing in the world, but still some amount of work.

### The MVC 3 way

Ok, what do you do in MVC 3? First of all, we still need our Registry, since that is how we setup StructureMap. But we don’t need our ControllerFactory at all! Instead, MVC 3 introduces a DependencyResolver with 3 methods:




``` csharp 
public static void SetResolver(IDependencyResolver resolver);
public static void SetResolver(object commonServiceLocator);
public static void SetResolver(Func<Type, object> getService,
                                   Func<Type, IEnumerable<object>> getServices);
```





So, instead of creating a `ControllerFactory`, you can create a `DependencyResolver`, which is more general, and only needs two simple methods: `GetService` and `GetServices`. However, an even easier way to do it, which I like, is to use the last overload. So, in `Application_Start`, we get:




``` csharp 
DependencyResolver.SetResolver(
    t =>
    {
        try { return ObjectFactory.GetInstance( t ); }
        catch { return null; }
    },
    t => ObjectFactory.GetAllInstances<object>().Where( s => s.GetType() == t )
);
```





And that’s all we need! And anywhere that MVC needs to resolve a dependency it will use this. And if it fails to resolve (i.e. returns null), it will just fall back to the default way. Me like!


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:437c2866-d672-4f7f-abb5-2d9854cd4544" class="wlWriterEditableSmartContent">Tags: [asp.net mvc](/tags/asp.net%20mvc), [dependency injection](/tags/dependency%20injection), [structuremap](/tags/structuremap)</div>
