---
title: "Powershell makes Windows Server AppFabric tolerable"
author: johan
date: 2010-06-30 21:00:32
updated: 2010-07-01 11:55:18
slug: powershell-makes-windows-server-appfabric-tolerable
tags: [AppFabric, Powershell]
---

I have been working quite a bit with [Windows Server AppFabric](http://msdn.microsoft.com/en-us/windowsserver/ee695849.aspx) lately, especially the WF Instancing. AppFabric is kind of nice, but unfortunately the GUI inside IIS is mind-numbingly slow! Luckily, there is a better and faster way of querying and working with AppFabric: [Powershell](http://www.microsoft.com/windowsserver2003/technologies/management/powershell/default.mspx).

It turns out that AppFabric comes with a whole bunch of Powershell cmdlets, that allows you to do just about anything you can do in the GUI (and lots of stuff you can’t do in the GUI). If you start Powershell Modules, and type in

``` powershell
> Get-Command -Module ApplicationServer
```

you will get a long list of available commands (or cmdlets).

[![A long list of available commands](/images/johan_driessen_se/WindowsLiveWriter/PowershellmakesWindowsServerAppFabrictol_12001/image_thumb.png "A long list of available commands")](/images/johan_driessen_se/WindowsLiveWriter/PowershellmakesWindowsServerAppFabrictol_12001/image_2.png)

I won’t go in to details, but rather just give you a very useful example of what you can do. When developing workflows, you get a lot of broken workflow instances in your instance store. You often need to find these, and delete them. You can do this in the GUI, of course, but it takes a long time. In Powershell, you can use the commands **[Get-ASAppServiceInstance](http://msdn.microsoft.com/en-us/library/ee767624%28v=WS.10%29.aspx)** and **[Remove-ASAppServiceInstance](http://msdn.microsoft.com/en-us/library/ee767631%28WS.10%29.aspx)**. First, we get all the instances for a specific web site:

``` powershell
> Get-ASAppServiceInstance -Sitename "Default Web Site"
```

[![List of workflow instances](/images/johan_driessen_se/WindowsLiveWriter/PowershellmakesWindowsServerAppFabrictol_12001/image_thumb_1.png "List of workflow instances")](/images/johan_driessen_se/WindowsLiveWriter/PowershellmakesWindowsServerAppFabrictol_12001/image_4.png)

Now, if just want to remove all persisted instances, we can do that by piping the results to **Remove-ASAppServiceInstance**:

``` powershell
> Get-ASAppServiceInstance -Sitename "Default Web Site" | Remove-ASAppServiceInstance
```

And just like that, all instances are gone! This is just a taste of what you can do, for more info on the commands, check out the [documentation on MSDN](http://msdn.microsoft.com/en-us/library/ee767662%28v=WS.10%29.aspx "AppFabric docs on MSDN"). It’s still a bit painful to work with AppFabric and WF, but this at least makes it tolerable.