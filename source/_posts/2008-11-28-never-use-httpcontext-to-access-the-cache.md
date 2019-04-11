---
title: "Never use HttpContext to access the Cache!"
author: johan
date: 2008-11-28 09:19:00
updated: 2009-10-09 23:51:25
slug: never-use-httpcontext-to-access-the-cache
tags: [caching, asp.net]
---

*This post was originally published on [http://labs.dropit.se/blogs](http://labs.dropit.se/blogs).*

This has probably been said a million times, but it's definitely worth saying again. Never use HttpContext.Current.Cache, always use HttpRuntime.Cache.

Why?

Because HttpRuntime is always available, even when you're not in a web context (e.g. even in a console app), and HttpContext is not. And it's the exact same Cache! Do you, for example, think that you have access to the HttpContext in a unit test? Probably not. HttpRuntime? Of course!

So, to sum it up:

``` csharp
//Bad code
return HttpContext.Current.Cache["myitem"];

//Good code
return HttpRuntime.Cache["myitem"];
```