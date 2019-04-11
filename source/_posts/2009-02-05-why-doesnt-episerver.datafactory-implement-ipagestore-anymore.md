---
title: "Why doesn't EPiServer.DataFactory implement IPageStore anymore??"
author: johan
date: 2009-02-05 13:48:00
updated: 2009-10-09 23:59:37
slug: why-doesnt-episerver.datafactory-implement-ipagestore-anymore
tags: [episerver]
---

*This post was originally published on *[*http://labs.dropit.se/blogs*](http://labs.dropit.se/blogs)*.*

We recently upgraded our project to EPiServer 5 R2, and that made some strange things happen. I had a class that uses the EPiServer.DataFactory to manipulate pages, and in order to make it testable, I had the following constructors:

``` csharp
private IPageStore _pageStore

public NovaImporter() : this( EPiServer.DataFactory.Instance )
{
}

public NovaImporter( IPageStore pageStore )
{
     _pageStore = pageStore;
}
```

This worked just fine before we upgraded. But now it turns out, in R2 DataFactory doesn't implement IPageStore anymore. In fact, the only interface it implements is IPageSource, which is pretty worthless in this context. Of course, DataFactory still have the same methods, so it *could *implement IPageStore, it just... doesn't. And to make things worse, DataFactory is **sealed**, so you can't really mock it at all, and it only implements PageStoreBase (which only contains event handlers), and the aforementioned IPageSource. So basically, EPiServer just made it impossible to write unit test for anything using the DataFactory. Thanks.

Why, oh why did EP decide to change this? Anyone know?