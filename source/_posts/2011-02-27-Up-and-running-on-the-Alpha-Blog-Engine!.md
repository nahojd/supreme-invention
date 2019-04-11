---
title: "Up and running on the Alpha Blog Engine!"
extid: posts/1025
author: johan
date: 2011-02-27 16:08:46
updated: 2011-02-27 16:08:46
slug: Up-and-running-on-the-Alpha-Blog-Engine!
tags: [alpha, blogging]
---

As I wrote in a post back in October, I’ve been writing my own blog engine, with the plan to run my blog on that instead. And today, finally, I was able to migrate my blog to my own blog engine, called Alpha! It took a little longer than I had hoped (the Devil is in the details…), and there are still lots of things I want to do on it, but it seems to work nicely, and I’m pretty happy with it!

A quick overview of the Alpha Blog Engine:

* Built in [ASP.NET MVC 3 with Razor](http://www.asp.net/mvc/mvc3)
* .NET 4 only
* [RavenDB](http://ravendb.net/) (that’s right, [no SQL](http://en.wikipedia.org/wiki/NoSQL) here!)
* [MetaWeblog API](http://www.xmlrpc.com/metaWeblogApi)
* Makes heavy use of third part libraries, e.g. [Elmah](http://code.google.com/p/elmah/), [FluentValidation](http://fluentvalidation.codeplex.com/), [StructureMap](http://structuremap.net/structuremap/), [Xml-Rpc.Net](http://www.xml-rpc.net/), [jQuery](http://jquery.com/), [HTML Agility Pack](http://htmlagilitypack.codeplex.com/), and [Argotic](http://argotic.codeplex.com/). Everything handled by [NuGet](http://nuget.codeplex.com/).
* Lightweight, i.e. no built-in comment function (uses [Disqus](http://disqus.com/) instead)
* HTML5, built on [Html5 Boilerplate](http://html5boilerplate.com/).

It’s built to be the perfect blog platform for a technical blogger, assuming that you can run it on a Windows Server, that is. Making it able to run on [Mono](http://www.mono-project.com/Main_Page) is on my to-do-list, though.

If anyone is interested, the source is available on bitbucket: [https://bitbucket.org/nahojd/alpha/overview](https://bitbucket.org/nahojd/alpha/overview "https://bitbucket.org/nahojd/alpha/overview")