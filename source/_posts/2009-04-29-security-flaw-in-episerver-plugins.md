---
title: "Security flaw in EPiServer plugins?"
author: johan
date: 2009-04-29 09:57:00
updated: 2009-10-10 00:07:52
slug: security-flaw-in-episerver-plugins
tags: [episerver, security]
---

*This post was originally published on *[*http://labs.dropit.se/blogs*](http://labs.dropit.se/blogs)*.*

A colleague of mine - let's call him Erik Nilsson, since that is his name - recently came across a potential security problem when creating your own edit och admin plugin to [EPiServer](http://www.episerver.com). Unfortunately, he's too shy to write a post about it, so I'll relay the information. If you create a plugin like this:

``` csharp
[EPiServer.PlugIn.GuiPlugIn(
  Area = EPiServer.PlugIn.PlugInArea.AdminMenu,
  DisplayName = "Plugin name.",
  Description = "Plugin desc.",
  Url = "~/plugin/myplugin.aspx",
  RequiredAccess = EPiServer.Security.AccessLevel.Administer )]
public partial class MyPlugin : EPiServer.UI.SystemPageBase
{
  //...
}
```

You might think that this plugin in only accessable to users with administration right. This however, is only the case if you access it through the admin mode. If you access the page directly, i.e. by using the url http://somesite.com/plugin/myplugin.aspx, you can access it without even having to login. Seems EPiServer only checks the RequiredAccess in the admin or edit mode.

So what you need to to in order to secure your plugin is to set required access for the "plugin" location in web.config:

``` xml
<location path="plugin">
  <system.web>
    <authorization>
      <allow roles="WebAdmins, Administrators" />
      <deny users="*" />
    </authorization>
  </system.web>
</location>
```

Bug or working as intended? I don't know, but it's important to keep in mind either way.

By the way, I'm trying out a new syntax hightlighter. Like it? It's [this one](http://alexgorbatchev.com/wiki/SyntaxHighlighter). The old one is still available as well, but it seems to be more happening with this one.