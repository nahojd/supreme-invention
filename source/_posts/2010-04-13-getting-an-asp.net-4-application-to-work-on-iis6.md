---
title: "Getting an ASP.NET 4 application to work on IIS6"
author: johan
date: 2010-04-13 18:26:18
updated: 2010-04-13 18:26:18
slug: getting-an-asp.net-4-application-to-work-on-iis6
tags: [.net 4, iis, iis6, asp.net mvc]
---

Now, for the last 5 months or so, I’ve been involved in a project where we develop an application with ASP.NET MVC on .NET 4 (we started on the beta 2). The main part of this application is hosted on servers running Windows 2008 Server R2 and IIS 7.5. But unfortunately, one part of the application has to be deployed to servers running Windows 2003 Server, and thus IIS 6.0.

Turns out, it’s a bit tricky to get .NET 4 working on IIS 6.0.

I did the usual stuff, I installed .NET 4 Framework, restarted the server, created a new application in IIS, running in its own Application Pool, and changed the ASP.NET version to 4. Then I tried to access my application. I was greeted by this:

[![The page cannot be found - WTF??](/images/johan_driessen_se/WindowsLiveWriter/GettinganASP.NET4applicationtoworkonIIS6_FDD4/image_thumb.png "The page cannot be found - WTF??")](/images/johan_driessen_se/WindowsLiveWriter/GettinganASP.NET4applicationtoworkonIIS6_FDD4/image_2.png) 

Strange. I’m pretty sure this is not what should happen. Now, since I’m running ASP.NET MVC, and have to do some mapping stuff on IIS6 ([described for example by Phil Haack back in 2008](http://haacked.com/archive/2008/11/26/asp.net-mvc-on-iis-6-walkthrough.aspx)), I naturally suspected this to be the problem. So I created a new application, and just added a default.aspx that prints the current date. Exactly the same result. Weird.

After some extensive googling (Yes, googling. Not Binging.) I got the tip to look at my IIS logfile to see what the error was, and I found this row:
  

``` plain 
2010-04-13 14:27:05 W3SVC674436196 127.0.0.1 GET / - 80 - 127.0.0.1   
Mozilla/4.0+(*snip*) 404 2 1260
```





So, the error is 404.2. A look at the [IIS status codes](http://support.microsoft.com/default.aspx/kb/318380?p=1) told me what this means: **404.2 – Lockdown policy prevents this request.** WTF? Well, it turns out that just because you install .NET 4 on Windows 2003 Server, you’re not automatically allowed to use it i IIS! The article provided a few useful links to the Knowledge base: 

*   [328505](http://support.microsoft.com/kb/328505/) How to list Web Server Extensions and Extension files in IIS 6.0 
*   [328360](http://support.microsoft.com/kb/328360/) How to enable and disable ISAPI extensions and CGI applications in IIS 6.0 



Following the instructions from the first of these, I checked what extensions where available on my server:

[![Picture of ASP.NET 4 not being allowed.](/images/johan_driessen_se/WindowsLiveWriter/GettinganASP.NET4applicationtoworkonIIS6_FDD4/image_thumb_1.png "Picture of ASP.NET 4 not being allowed.")](/images/johan_driessen_se/WindowsLiveWriter/GettinganASP.NET4applicationtoworkonIIS6_FDD4/image_4.png) 

Notice that the Status for the .NET 4 ASP.NET ISAPI extension is 0? Well, that means that it is disabled. Now, if you read the other knowledge base article, it will tell you how to enable it. Just run the following command:




``` plain 
cscript iisext.vbs /EnFile C:\WINDOWS\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll
```





And If I now run the /ListFile command again, I will se that the ASP.NET 4 extension is now showing a little “1” as status, telling me that it is now enabled.

[![Yay, ASP.NET 4 is enabled!](/images/johan_driessen_se/WindowsLiveWriter/GettinganASP.NET4applicationtoworkonIIS6_FDD4/image_thumb_2.png "Yay, ASP.NET 4 is enabled!")](/images/johan_driessen_se/WindowsLiveWriter/GettinganASP.NET4applicationtoworkonIIS6_FDD4/image_6.png) 

And after this, my application worked fine. And not only the test application that printed today's date, but even my ASP.NET MVC application! 


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:ceaeb9e1-a5dc-4b72-a13d-895c9023642b" class="wlWriterEditableSmartContent">Tags: [.net 4](/tags/.net%204), [iis](/tags/iis), [iis6](/tags/iis6), [asp.net mvc](/tags/asp.net%20mvc)</div>
