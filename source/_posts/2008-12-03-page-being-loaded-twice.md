---
title: "Page being loaded twice"
author: johan
date: 2008-12-03 09:08:00
updated: 2009-10-09 23:53:07
slug: page-being-loaded-twice
tags: [asp.net]
---

*This post was originally published on [http://labs.dropit.se/blogs](http://labs.dropit.se/blogs). *

Ever had the problem of pages being loaded twice, that is that all the events are being hit two times?

I have, and I've never really been able to figure out why, but I recently stumbled over [this old entry](http://aspnetresources.com/blog/page_events_raised_twice.aspx) at ASP.NET Resources. It seems that it is a problem with AutoEventWireup, or rather it is a problem if you use both AutoEventWireup *and *wire the events in the codebehind as well. You can read the full story on ASP.NET Resources, but it seems that it is obviously very important to be consistent in your choice of using AutoEventWireup or overriding the methods, and *not *using both of them!

I know we have this problem in our current project, so I guess I'd better track down all Page_Something-methods and AutoEventWireups and turn them off. Seems to be the easiest way to get rid of this.