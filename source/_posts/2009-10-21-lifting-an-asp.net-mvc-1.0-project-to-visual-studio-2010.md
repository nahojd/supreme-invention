---
title: "Lifting an ASP.NET MVC 1.0 project to Visual Studio 2010"
author: johan
date: 2009-10-21 09:40:40
updated: 2009-11-18 22:28:39
slug: lifting-an-asp.net-mvc-1.0-project-to-visual-studio-2010
tags: [asp.net, asp.net mvc, visual studio]
---

I have a pet project (several actually, but let’s stick to one of them). It’s called *Önskelistemaskinen*, and is a web application for keeping track of the wish lists of my family members. It’s build in [ASP.NET MVC](http://asp.net/mvc) with a [Linq-to-SQL](http://msdn.microsoft.com/en-us/library/bb425822.aspx) backend (loosely modelled after [Rob Conery’s Storefront project](http://blog.wekeroad.com/category/mvc-storefront/)), and naturally grossly overdesigned for an application with a target audience of about 10 people. 

Anyway, I started working on this when the first beta of ASP.NET MVC came out about a year ago, and with every new release I have had to rework lots of it. So when both [Visual Studio 2010 beta 2](http://msdn.microsoft.com/en-us/vstudio/dd582936.aspx) and [ASP.NET MVC 2 preview 2](http://haacked.com/archive/2009/10/01/asp.net-mvc-preview-2-released.aspx) came out almost simultaneously I figured, what the hell, time for another rewrite!

My first approach was to just open the project in Visual Studio 2010. It seemed very helpful, offering to convert the project to VS2010. But on closer inspection, I found that it had converted all of the projects except for the actual web project. So no luck.

I had read a blog post by [Scott Hanselman](http://www.hanselman.com) about converting a ASP.NET MVC 1.0 project to 2.0 (for Visual Studio 2008, though), and also thought I had seen something pass by about an application doing that for you. I figured that might be the way to go. Just to be sure I threw the question out on [Twitter](http://twitter.com/nahojd), and within minutes I got an answer pointing to the [conversion application](http://weblogs.asp.net/leftslipper/archive/2009/10/19/migrating-asp-net-mvc-1-0-applications-to-asp-net-mvc-2.aspx "Migrating ASP.NET MVC 1.0 applications to ASP.NET MVC 2"). 

I used the application and converted my 1.0 project to 2.0. Then I opened the project in VS2010 again, and this time the conversion wizard gladly converted my web project to VS2010! Now, I had a few other problems, since I hade used a few features in MVC Futures library, which didn’t seem to work anymore. But after fixing these, everything seems to work fine (by the way, I noticed while writing this post that MVC Futures also has a new release, so a better idea might have been to use that one. I’ll check it out tonight).

In conclusion, to get your VS2008/ASP.NET MVC 1.0 project to work in Visual Studio 2010, follow these steps:

1. Upgrade the project to ASP.NET MVC 2, preferably using [Eilon Lipton’s migration tool](http://weblogs.asp.net/leftslipper/archive/2009/10/19/migrating-asp-net-mvc-1-0-applications-to-asp-net-mvc-2.aspx). 
2. Open the project in Visual Studio 2010, and let the conversion wizard work its magic. 
3. ?? 
4. Profit!   

*Update: Turns out that Phil Haack wrote a [post describing this](http://haacked.com/archive/2009/10/20/vs10beta2-and-aspnetmvc.aspx) sometime between my attempts to get it working yesterday evening and my writing of this post this morning.*