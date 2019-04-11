---
title: "Accessing an IIS Express site from a remote computer"
extid: posts/6145
author: johan
date: 2011-08-16 16:45:01
updated: 2015-10-15 08:44:11
slug: Accessing-an-IIS-Express-site-from-a-remote-computer
tags: [iis express, visual studio, asp.net, internet explorer]
---

Sometimes (waaaay to often) I have to check that a site I’m working on looks like it should in Internet Explorer 6, Safari on Mac or some other browser that I can’t run in Windows 7. In this case I wanted to access it from [IE6 running in XP Mode](/posts/windows-7-xp-mode-is-really-nice). I could of course deploy it to IIS and make it publicly available, but since I’m now using [IIS Express](http://learn.iis.net/page.aspx/868/iis-express-overview/) for running my sites from Visual Studio instead of the built-in web server Cassini, it almost simple to let other computers on my network access the site.

[This post by Scott Hanselman](http://www.hanselman.com/blog/WorkingWithSSLAtDevelopmentTimeIsEasierWithIISExpress.aspx "Working with SSL at Development Time is easier with IISExpress") almost describes how to do it, but since I had to make some adjustments I thought I might write a shorter post with just the steps you need for this.

## 1 – Bind your application to your public IP address

Normally when you run an application in IIS Express, it’s only accessible on http://localhost:[someport]. In order to access it from another machine, it needs to be bound to your public IP address as well. Open* D:\Users[YourName]\Documents\IISExpress\config\applicationhost.config *and find your site.

**UPDATE FOR VISUAL STUDIO 2015:** As was pointed out to me in a comment by Søren Nielsen, in Visual Studio 2015 the IIS Express configuration files have moved. They are now separate per project, and stored in */{project folder}/.vs/config/applicationhost.config*. Which is much better, in my opinion, just don't forget to add .vs/ to your *.gitignore/.hgignore* files!

You will find something like this:

``` xml
<site name="Alpha.Web" id="2">
    <application path="/">
        <virtualDirectory path="/" physicalPath="C:\Users\Johan\HgReps\Alpha\Alpha.Web" />
    </application>
    <bindings>
        <binding protocol="http" bindingInformation="*:58938:localhost" />
    </bindings>
</site>
```

In `<bindings>`, add another row:

`<binding protocol="http" bindingInformation="*:58938:192.168.1.42" />` (But with your IP, and port number, of course)

## 2 - Allow incoming connections

If you’re running Windows 7, pretty much all incoming connections are locked down, so you need to specifically allow incoming connections to your application. First, start an administrative command prompt. Second, run these commands, replacing 192.168.1.42:58938 with whatever IP and port you are using:

`> netsh http add urlacl url=http://192.168.1.42:58938/ user=everyone`

This just tells http.sys that it’s ok to talk to this url.

`> netsh advfirewall firewall add rule name="IISExpressWeb" dir=in protocol=tcp localport=58938 profile=private remoteip=localsubnet action=allow`

This adds a rule in the Windows Firewall, allowing incoming connections to port 58938 for computers on your local subnet.

And there you go, you can now press Ctrl-F5 in Visual Studio, and browse you site from another computer!
