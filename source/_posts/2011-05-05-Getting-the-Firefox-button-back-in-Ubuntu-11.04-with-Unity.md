---
title: "Getting the Firefox button back in Ubuntu 11.04 with Unity"
extid: posts/4097
author: johan
date: 2011-05-05 16:50:34
updated: 2011-05-05 16:50:34
slug: Getting-the-Firefox-button-back-in-Ubuntu-11.04-with-Unity
tags: [ubuntu, firefox, unity]
---

Since I've been trying to learn more about [Ruby on Rails](http://rubyonrails.org/) (everybody else seems to be doing it, so why shouldn't I?) lately, I've been spending quite some time in [Ubuntu](http://www.ubuntu.com/) instead of my usual Windows 7 environment. And since that's the way I do things, I immediately upgraded to [11.04](http://www.ubuntu.com/ubuntu/whats-new) when it was released.

With Ubuntu 11.04 you get [Unity](http://unity.ubuntu.com/) instead of [Gnome](http://www.gnome.org/), which is pretty cool. I generally like it, especially the fact that you can, just like I do in Win7, just press the Windows key and type a few letters to start a program instead of having to find it in the menu. A new feature that I'm not so impressed with, though, is the fact that they have moved the menus up to the top, instead of having them in the application windows, just like in Mac OS. I mostly find it confusing (it’s just way to easy to have focus on the wrong window and using the wrong menu – no usability there) and unnecessary, but I suppose I can live with it.

Except in Firefox 4. I've been using it since the early betas, and I've just recently gotten used to, and actually started liking, the Firefox button with the combined menu. And bam - the old menus are back in Unity!

Luckily, it turns out there's an easy way to restore law and order to the galaxy:

`$ sudo apt-get remove firefox-globalmenu`

And just like that, the Firefox button is back, and the menus in the top bar are gone!
<div style="margin: 0px; padding: 0px; float: none; display: inline;" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:827706e6-5d9c-48be-8814-a960b0317aec" class="wlWriterEditableSmartContent">Tags: [ubuntu](/tags/ubuntu), [firefox](/tags/firefox), [unity](/tags/unity)</div>
