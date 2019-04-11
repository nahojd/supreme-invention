---
title: "Simple Exception logging with ELMAH"
author: johan
date: 2009-04-19 18:52:00
updated: 2009-10-10 00:05:09
slug: simple-exception-logging-with-elmah
tags: [tools, logging, elmah, errors]
---

*This post was originally published on *[*http://labs.dropit.se/blogs*](http://labs.dropit.se/blogs)*.* 

Logging errors on a web site can be a pain. Sure, when you use [EPiServer](http://www.episerver.com), you get the [log4net](http://logging.apache.org/log4net/index.html) error logging framework, but how much fun is it to read the log files it produces? First, every 404 is going to clog the log file making the important errors hard to find and second, the format of the log is not really reader friendly – you need to access the server and open it in a text editor. This usually means that you (or I, or we) ignore the error log until something bad happens. 

At the same time, there is much to be gained by keeping track of your exceptions continually, and fixing them before they cause problems. And it turns out there is an excellent tool for this, called [Elmah](http://code.google.com/p/elmah/). 

Elmah is an error logging tool that is completely pluggable, meaning you can add it without re-compiling och re-deploying, just by dropping a dll in the /bin directory and adding a few lines to the web.config file. After doing this, you get a nice web interface for you exceptions, that look like this: 
 [![image.axd](/images/blog_driessen_se/subtext/WindowsLiveWriter/SimpleExceptionloggingwithELMAH_1266E/image.axd_thumb.png "image.axd")](/images/blog_driessen_se/subtext/WindowsLiveWriter/SimpleExceptionloggingwithELMAH_1266E/image.axd_2.png)   

What you see here is just the list of errors. If you click the details link on one of the errors, you get lots of more information. First of all, you get info about the error itself, the stack trace and so on. Basically the stuff you see on the usual yellow .NET error page. But you also get all the server variables, such as the user agent, the charset, the language, the hostname, the file that was accessed and so on. This makes it much easier to track down the actual problem, and this is of course something you don't get when you just use log4net and log to a file. Elmah logs to memory by default, meaning that the log disappear when you restart the application. But it can also log to a database or and XML-file if you need persistance. Regardless of the format, the UI is the same. 

It took me about 2 minutes getting this up and running, following [this nice tutorial](http://code.google.com/p/elmah/wiki/DotNetSlackersArticle). And since this seemed really sweet, I immediately wanted to install it on a real server (no, Apollo is not a real server, it’s just my computer at home). Said and done, I quickly discovered two things: 

1.  On a Windows 2008 Server, you don’t put you httpmodules and httphandlers under the system.web element, but under the system.webserver element. What’s more, httphandlers need a name attribute. 
2.  If you log exceptions on a public web site instead of you own toy site, 99% of them will be HttpExceptions, namely 404 errors (actually, I knew that already, I mentioned it in the first paragraph).   

The first problem was easily fixed. The second one was more interesting, as this was one of the reasons that I wanted to use Elmah in the first place. Well, luckily for me, it turns out that Elmah also comes with a [pretty competent filtering framework](http://code.google.com/p/elmah/wiki/ErrorFiltering). So by using this little snippet, I filtered out all 404 errors: 
  

``` xml 
<!-- register the filter module in httpModules or modules, depending on the IIS version -->
<add type="Elmah.ErrorFilterModule, Elmah" name="ErrorFilter" />

<!-- config the filter in the elmah config section -->
<errorfilter>
    <test>
        <equal type="Int32" value="404" binding="HttpStatusCode" />
    </test>
</errorfilter>
```





So now the exception logging is running happily both on my toy site and on the production web site, whose identity I will not disclose here. Unfortunately, since I got it up and running about an hour ago, not a single exception has occurred! Somebody better do something we didn’t think of soon! :-) 

Meanwhile, I will add the built in RSS-feed to my RSS-reader. I did mention that it comes with a built-in RSS feed, did I not? 


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:fc41eb77-384f-4385-bd35-a5e29a5d3e78" class="wlWriterSmartContent">Tags: [tools](/tags/tools), [logging](/tags/logging), [elmah](/tags/elmah), [errors](/tags/errors)</div>
