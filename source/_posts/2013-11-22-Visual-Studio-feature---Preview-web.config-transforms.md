---
title: "Visual Studio feature - Preview web.config transforms"
extid: posts/9569
author: johan
date: 2013-11-22 12:36:27
updated: 2013-11-22 12:42:02
slug: Visual-Studio-feature---Preview-web.config-transforms
tags: [visual studio, asp.net]
---

On my way to [continuous delivery](https://en.wikipedia.org/wiki/Continuous_Delivery), I often use Visual Studios built-in support for [Web.config transformations](http://msdn.microsoft.com/en-us/library/dd465326%28v=vs.110%29.aspx), at least for relatively simple situations. This allows you to automatically create variations of you web.config file for different deployment environments. And with the help of the excellent Visual Studio plug-in [SlowCheetah](http://visualstudiogallery.msdn.microsoft.com/69023d00-a4f9-4a34-a6cd-7e854ba318b5), you can apply this to other config files as well.

This is all great, but I found it a little tricky to verify that my config transformations yielded the expected result, I basically had to create the deploy packages and check inside them. But then I noticed this little gem in Visual Studio:

![Yes, there is a preview transform option in the context menu for transform.config-files!](/images/preview-transform-menu.png)

If you right-click on a config transformation file, you get the "Preview transform" option. This was, I think, introduced in Visual Studio 2012. I was just a little slow to notice it. But it's great! If you select it, you get to see the original config file and the transformed file side by side, with all the changes highlighted.

[![Click me if you can't read! :-)](/images/preview-transform-thumbnail.png)](/images/preview-transform.png)

All the differences between the files are highlighted on the right, and also in the code window itself. This really makes it very easy to verify that the transforms are correct.

Tags: [visual studio](../tags/visual studio), [asp.net](../tags/asp.net), [continuous delivery](../tags/continuous delivery)
