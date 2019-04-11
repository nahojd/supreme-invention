---
title: "Bleeding edge"
author: johan
date: 2009-11-09 13:01:37
updated: 2009-11-09 13:01:37
slug: bleeding-edge
tags: [asp.net mvc, ioc, structuremap, .net 4]
---

Starting a new project at work today. For once this project will have to use the latest and greatest technology. We want to use the new [Windows Workflow Foundation](http://msdn.microsoft.com/en-us/netframework/aa663328.aspx), which means that we have to use [.NET 4 and Visual Studio 2010](http://msdn.microsoft.com/en-us/vstudio/dd582936.aspx). We will also use ASP.NET MVC, and since we are using VS2010 beta 2, that means [ASP.NET MVC 2 Preview 2](http://www.microsoft.com/downloads/details.aspx?familyid=D3F06BB9-5F5F-4F46-91E9-813B3FCE2DB1&displaylang=en). Nice!

There are some difficulties, though. For example, we are planning to use [StructureMap](http://structuremap.sourceforge.net/Default.htm) as for dependency injection, and since [MvcContrib](http://mvccontrib.codeplex.com/) has a built-in Controller Factory for StructureMap, we wanted to use that too (it also has a lot of other neat features). Well, turns out that the release versions of MvcContrib don’t support ASP.NET MVC 2. So, I hade to pull the latest nightly from [source control](http://teamcity.codebetter.com/viewType.html?buildTypeId=bt83&tab=buildTypeStatusDiv). That solved the StructureMap problem, but will it yield new problems? Time will tell.

It’s always interesting to work on the bleeding edge of technology. My new motto is that anything out of beta is too old to be worth running! :-)