---
title: "Unit testing saving pages in EPiServer CMS 6"
author: johan
date: 2010-08-11 16:48:01
updated: 2010-08-11 16:48:01
slug: unit-testing-saving-pages-in-episerver-cms-6
tags: [episerver, tdd, dependency injection, ioc, moq, structuremap, reflection]
---

I’ve been working quite a lot with [EPiServer](http://world.episerver.com/Documentation/Categories/EPiServer-CMS-Version/EPiServer-CMS/) in the last six years or so, and unit testing has always been a pain in the ass (not that I was writing unit tests anyway six years ago, I hardly knew what I was doing back then… can’t believe I actually got paid! :-)). And it still is, even in the latest version.

Luckily there are some [EPiServer developers](http://world.episerver.com/Blogs/) that are either smarter than me, or just have more free time, for example [Joel Abrahamson](http://joelabrahamsson.com/) who has created a wrapper project, [EPiAbstractions](http://github.com/joelabrahamsson/EPiAbstractions), for wrapping the unmockable EPiServer classes in mockable classes and interfaces. Wonderful!

We started using this in our current project recently, and mostly it works fine. Instead of using [EPiServer.DataFactory](http://sdk.episerver.com/library/cms6/html/T_EPiServer_DataFactory.htm) inside our business classes, we now inject *IDataFactoryFacade* from EPiAbstractions, and use that instead. Like this:

``` csharp
//Old way
public class Foo
{
    public PageData GetPageById(int id)
    {
        return EPiServer.DataFactory.Instance.GetPage(
            new PageReference(id));
    }
}

//New way
public class Foo
{
    private IDataFactoryFacade dataFactory;

    public Foo(IDataFactoryFacade dataFactory) {
        this.dataFactory = dataFactory;
    }

     public PageData GetPageById(int id)
    {
        return dataFactory.GetPage(new PageReference(id));
    }
}
```

Simple enough, it let’s us mock the DataFactory with [Moq](http://code.google.com/p/moq/wiki/QuickStart), and we can use an IoC container like [StructureMap](http://structuremap.github.com/structuremap/index.html) to inject the the DataFactory class, or we can create a constructor overload that uses *DataFactoryFacade.Instance*.

## The problem

So, with all these nice interfaces that we are able to mock, what is the problem? Well, I was trying to write a test for this method:

``` csharp
public void SetAnmälanTillåten(int verksamhetPageId, bool anmälanTillåten)
{
    var page = _dataFactory.GetPage(new PageReference(verksamhetPageId));
    if (page == null)
        return;

    var writeablePage = page.CreateWritableClone();
    writeablePage.Property["TillåtAnmälan"].Value = anmälanTillåten;
    _dataFactory.Save(writeablePage, EPiServer.DataAccess.SaveAction.Publish, EPiServer.Security.AccessLevel.NoAccess);
}
```

This is a pretty simple method, it gets a page from the DataFactory, sets a property, and saves it again. So we just need to mock *GetPage* and *Save*, right? I tried it, and I got this error:

``` plain
The type initializer for 'EPiServer.Web.PermanentLinkMapStore' threw an exception.
System.TypeInitializationException
EPiServer.Web.PermanentPageLinkMap Find(EPiServer.Core.PageReference)
System.Guid get_GuidValue()
EPiServer.Core.PropertyData CreateWritableClone()
EPiServer.Core.PropertyDataCollection CreateWritableClone()
EPiServer.Core.PageData CreateWritableClone()
```

GetPage was mocked like this (somewhat simplified):

``` csharp
var page = new PageData();
page.Property.Add( "PageName", new PropertyString( "Name" ) );
page.Property.Add( "PageLink", new PropertyPageReference(
    new PageReference( 4711 ) ) );
page.Property.Add( "TillåtAnmälan", new PropertyBoolean() );
dataFactoryMock.Setup( d => d.GetPage(
    It.Is<PageReference>( p => p.ID == 4711 ) ) ).Returns( page );
```

So, what is the problem? Well, a [look inside EPiServer.dll](http://www.red-gate.com/products/reflector/ "You really can't work with EPiServer without using Reflector...") told me that when calling *CreateWriteableClone* for the *PageData* object, it in turn calls *CreateWriteableClone* for each property in the *PropertyDataCollection*. When it comes to the *PageLink* property, it calls *PropertyPageReference.CreateWriteableClone*:

![PropertyPageReference.CreateWriteableClone() implementation from EPiServer CMS 6](/images/johan_driessen_se/WindowsLiveWriter/UnittestingsavingpagesinEPiServerCMS6_E1E4/Capture_1.png "PropertyPageReference.CreateWriteableClone() implementation from EPiServer CMS 6")

This doesn’t look so bad, does it? But the problem is in *this.GuidValue*:

![PropertyPageReference.GuidValue implementation from EPiServer CMS 6](/images/johan_driessen_se/WindowsLiveWriter/UnittestingsavingpagesinEPiServerCMS6_E1E4/Capture2_3.png "PropertyPageReference.GuidValue implementation from EPiServer CMS 6")

So, unless the field *_pageGuid* has a non-empty value, it will call *PermanentLinkMapStore.Find*, and we will get the error. Unfortunately, since the setter won’t let us manually set the value without calling *Modified()*, which will also make external calls, we can’t use it to set the value.

## The solution

[System.Reflection](http://msdn.microsoft.com/en-us/library/system.reflection.aspx) to the rescue! With a little helper method for setting the private field, I was able to get the test working:

``` csharp
private static void SetPrivateField<T>( object o, string fieldName, T newValue )
{
    Type type = o.GetType();
    BindingFlags privateBindings = BindingFlags.NonPublic | BindingFlags.Instance | BindingFlags.Static;
    FieldInfo field = type.GetField( fieldName, privateBindings );
    field.SetValue( o, newValue );
}

//Mocking code
var page = new PageData();
page.Property.Add( "PageName", new PropertyString( pageName ) );
var propertyPageReference = new PropertyPageReference( new PageReference( 4711) );
SetPrivateField<Guid>( propertyPageReference, "_pageGuid", Guid.NewGuid() );
page.Property.Add( "PageLink", propertyPageReference );

dataFactoryMock.Setup( d => d.GetPage( It.Is<PageReference>( p => p.ID == 4711) ) )
    .Returns( page );
```

And, yay, we have a working test! (Of course, the actual test also had to do a setup for Save, but I’ll leave that as an exercise for the reader.

Finally, there may well be an easier way to do this. If so, I would be most interested to hear of it! Now, I’ll get back to writing unit tests for my project (we still have pretty bad code coverage…). I have a feeling I’ll have to revisit this topic in the future, as more interesting mocking scenarios appear, though.