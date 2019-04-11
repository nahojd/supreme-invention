---
title: "Subtle differences"
author: johan
date: 2009-08-26 15:08:00
updated: 2009-10-10 00:17:21
slug: subtle-differences
tags: [asp.net, iis, errors]
---

*This post was originally published on *[*http://labs.dropit.se/blogs*](http://labs.dropit.se/blogs)*.*

Today I hade the dubious pleasure of having to work with an old EPiServer 4 site, and having to deploy it to IIS7 on a Windows 2008 Server.

In order to make everything work in EPiServer 4, you need to make sure that all 404-request are redirected to /Util/NotFound.aspx. On my development machine, which runs Windows 7, I accomplished this by adding the following code to web.config:

``` xml
<system.webServer>
    <httpErrors>
        <remove statusCode="404" subStatusCode="-1" />
        <error statusCode="404" prefixLanguageFilePath="" path="/Util/NotFound.aspx" responseMode="ExecuteURL" />
    </httpErrors>
</system.webServer>
```

Since both Windows 7 and Windows 2008 Server runs IIS7, you would think that this should work on the server as well. But when I ran a particular URL that needed the redirection to NotFound, I only got the following error message:

*The page cannot be displayed because an internal server error has occurred.*

Nothing else. Nothing in the log files, nothing in the event log. Hmmm.

After fiddling around for a while and trying to figure out what was wrong, I tried going into the Error Pages feature in IIS Manager on the server, and suddenly got the error message saying:

*This configuration section cannot be used at this path. This happens when the section is locked at a parent level. Locking is either by default (overrideModeDefault="Deny"), or set explicitly by a location tag with overrideMode="Deny" or the legacy allowOverride="false".*

What this basically means is that I can’t use the `<httpErrors>`-section in web.config, because the machine settings says that I can’t. Now, the settings for IIS7 can be found in the file *applicationHost.config*, located in *C:\Windows\System32\inetsrv\config*. A look in this file on both the server and on my machine showed a subtle difference:

``` xml
<!-- Windows 7 -->
<section name="httpErrors" overrideModeDefault="Allow" />

<!-- Windows 2008 Server -->
<section name="httpErrors" overrideModeDefault="Deny" />
```

For some reason, the default configuration on Windows 2008 Server doesn’t let you override the httpErrors-configuration in web.config, while the default configuration on Windows 7 does. I’m not saying that either one is more correct that the other, but if it had been the same on both it would have saved me a couple of hours of work today. Sometimes subtle differences can cause a lot of work…