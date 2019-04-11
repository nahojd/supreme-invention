---
title: "Fun with betas and RC of .NET 4 and AppFabric"
author: johan
date: 2010-02-18 20:47:17
updated: 2010-02-25 15:44:14
slug: fun-with-betas-and-rc-of-.net-4-and-appfabric
tags: [.net 4, appfabric, WF4]
---

*UPDATE: Turns out there is a less difficult way to do this. The uninstaller just looks for the config file of .NET 4 beta 2 (v4.0.21006), so all you need to do is copy you machine.config and web.config from \Windows\Microsoft.NET\Framework(64)\v4.0.30128\Config to ..\v4.0.21006\Config (you probably have to create the folder), and uninstall it. It is explained in greater detail in [this post](http://developers.de/blogs/damir_dobric/archive/2010/02/19/uninstalling-of-appfabric-beta-1.aspx). So my weekend was saved.*

Although I wouldn’t dare complain about the fact that I get to use all the latest Microsoft-technology in my current project, sometimes it can be troublesome.

As I have mentioned in earlier posts, we are building an application in ASP.NET MVC on .NET 4, primarily because we want to use Workflow Foundation 4. Since we will be using long running workflows, we need to persist them, and it seems the good way to do that is using the new [Windows Server AppFabric](http://msdn.microsoft.com/en-us/windowsserver/ee695849.aspx) (previously codename Dublin), which also gives us nice monitoring features for WCF.

Now, we started out using VS2010 and .NET 4 beta 2, and about a month ago we installed the beta 1 of AppFabric. As expected, betas are a little buggy, and when [the Release Candidate for VS2010 and .NET 4](http://msdn.microsoft.com/en-us/vstudio/dd582936.aspx) was released, naturally we wanted to upgrade. So we did. Everything went smoothly, some small changes in the MVC projects, but nothing major. Until we tried AppFabric. We kept getting this error in the AppFabric Dashboard in IIS:

“The configuration section ‘microsoft.applicationServer/monitoring’ cannot be read because it is missing a section declaration”

This rang a bell, since this is the exception you get if your application pool is not running .NET 4, but that was not the case. Well, to make a long story short, after some research, we came across a post on the MSDN AppFabric forum. Seems like [AppFabric beta 1 won’t run on .NET 4 RC](http://social.msdn.microsoft.com/Forums/en-SG/dublin/thread/03169207-dbcb-48cb-8b8a-de674fdcac31) at all. It just isn’t supported. And a new beta that will run on RC will be released “soon”.

So basically, we have to make do without persistence until that happens. Our next sprint demo is on the 1st of March, and unless the new beta is released well before that, we will have to be **very** careful not to recycle our app pool during the demo! :-)

On a finishing note, since AppFabric didn’t work anyway, I tried to uninstall it. Unfortunately, that won’t work either. A helpful reply to my reply on [the aforementioned post](http://social.msdn.microsoft.com/Forums/en-SG/dublin/thread/03169207-dbcb-48cb-8b8a-de674fdcac31) on the MSDN forum explained what I have to do:

1. Uninstall VS2010 RC
2. Install VS2010 beta 2
3. Uninstall AppFabric beta 1
4. Uninstall VS2010 beta 2
5. Install VS2010 RC

Sounds like fun… Maybe I’ll try it this weekend. *Nope, didn’t have to. See top. :-)*