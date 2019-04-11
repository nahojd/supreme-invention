---
title: "Simple source control with Subversion and Dropbox"
author: johan
date: 2009-01-14 16:41:00
updated: 2009-01-14 16:41:00
slug: simple-source-control-with-subversion-and-dropbox
tags: [dropbox, tools, svn]
---

*This post was originally published on [http://labs.dropit.se/blogs](http://labs.dropit.se/blogs). *

I noticed that the activity on this blog has been a bit low lately, probably due to the fact that everyone has been away for the holidays. Instead of nagging on everyone to start writing again, I though I should lead by example and write an entry myself first. :-)

This is on the same theme as my previous entry, [Keeping things in sync](http://labs.dropit.se/blogs/post/2008/11/26/Keeping-things-in-sync.aspx), where I went on about how happy I am with [Dropbox](http://labs.dropit.se/blogs/admin/Pages/www.getdropbox.com) and [Evernote](http://labs.dropit.se/blogs/admin/Pages/www.evernote.com) to have access to all my important files and notes on all computers. Since then, I have discovered another great way of using Dropbox.

I usually work on a few projects outside of work, and although I do want source control on these (mainly for the versioning), I can't put these in our TFS. So I did what every responsible developer does; set up a [VisualSVN Server](http://www.visualsvn.com/server/) on my computer and use that one for the verison control (I use [AnkhSVN](http://ankhsvn.open.collab.net/) for the VS integration by the way, works almost flawlessly for me). But now it happens that I have another computer at home, one that is faster (dual core E8400) and has a bigger screen (24"), and when I'm at home I like to use that one for programming instead of the laptop I use at work (Thinkpad X301, lots of name dropping here). One way is to turn on my laptop and connect to the server there. But that seems like a lot of work. You know, turning on the computer, finding out what IP the DHCP has assigned to it today and so on. And what if I left my computer at work?

Enter Dropbox. Again. I simply created a new folder in my dropbox folder, called SVN-repositories. Then I installed VisualSVN Server on both computers, and set it up to use the SVN-repositories folder to store the repositories. Done!

So now I have a local SVN server on both computers, that automagically keep in sync. If I do some work on one computer, the next time I start the other one Dropbox will inform me that it has updated a few obscure SVN-files, and that's it. Wunderbaum!

A word of warning though. I have no idea what would happen if you were to change data in both repositories at the same time. Probably something bad. Wouldn't try it.