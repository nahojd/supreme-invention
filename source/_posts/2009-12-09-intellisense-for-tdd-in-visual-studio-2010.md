---
title: "Intellisense for TDD in Visual Studio 2010"
author: johan
date: 2009-12-09 11:20:11
updated: 2009-12-09 11:33:19
slug: intellisense-for-tdd-in-visual-studio-2010
tags: [Tdd, Visual Studio]
---

While I’m trying to get the time to write a longer post about lessons learned working with [ASP.NET MVC 2](http://asp.net/mvc) and [VS2010](http://msdn.microsoft.com/en-us/vstudio/dd582936.aspx), I thought I’d throw a shorter one out there in the meantime.

Last week I was att [The Gu’s](http://weblogs.asp.net/scottgu/) presentation in Stockholm, and while he said a lot of interesting things about ASP.NET 4 and ASP.NET MVC (and some rather uninteresting things in his sales pitch for Silverlight 4), one thing in particular caught my attention: A new [intellisense mode for TDD in Visual Studio 2010](http://msdn.microsoft.com/en-us/library/dd547188(VS.100).aspx)!

The standard intellisense in Visual Studio is a little “too good” to work well in a TDD scenario. When I write tests for classes and methods that I haven’t written yet, it happily suggests the closest match (like the test class itself for a class name).

![No, stupid Intellisense! I don't want to create an instance of the test class!](/images/johan_driessen_se/WindowsLiveWriter/IntellisenseforTDDinVisualStudio2010_9C04/image8.png "No, stupid Intellisense! I don't want to create an instance of the test class!")

Annoying. But in Visual Studio 2010, you can change the intellisense mode to “TDD friendly”, just by pressing **Ctrl-Alt-Space** (and back again, of course)! And instead of the annoying behaviour pictured above, you get this nice and helpful behaviour:

![Yes, helpful Intellisense! I do want to create an instance of a class that doesn't exist, thanks for understanding!](/images/johan_driessen_se/WindowsLiveWriter/IntellisenseforTDDinVisualStudio2010_9C04/image4_1.png "Yes, helpful Intellisense! I do want to create an instance of a class that doesn't exist, thanks for understanding!")

And of course, if I wanted a FooControllerTests instance, I could just press the down arrow and Enter to select it. A small feature, but extremely helpful when doing TDD.

Man, I really suck at writing short posts…