---
title: "Keeping things in sync part 2 – Dropbox and Junction"
author: johan
date: 2010-03-03 21:04:26
updated: 2010-03-03 21:05:27
slug: keeping-things-in-sync-part-2-ndash-dropbox-and-junction
tags: [dropbox, windows 7, blogging]
---

This is kind of a follow-up to my ancient post from november 2008, [Keeping things in sync](/archive/2008/11/26/keeping-things-in-sync.aspx). I still use more than one computer, three to be precise; an Thinkpad X301 at work, a workstation of sorts at home, and a Netbook in front of the TV. And I want the switch between them to go as smoothly as possible. So, just as I did in 2008, I use [Dropbox](http://dropbox.com) to store all my documents. Great!

But, as it turns out, not all applications lets you choose where to store the data. About 15 minutes ago, I sat myself down in front of my computer at home, to start writing a blog post, a tutorial to Windows Workflow Foundation 4. I started [Windows Live Writer](http://download.live.com/writer) (which is a great app for writing the blog posts), and suddenly recalled that I hade already started on that post, but on my laptop.

Live Writer stores its data (posts and drafts) in a a folder called “My Weblog Posts” in the users “Documents” folder. That is not configurable. But I really would like to keep it in my dropbox instead. If only there was a way…

Wait, there is! Junction to the rescue! As it turns out, Windows (or rather NTFS) has been supporting directory symbolic links, or symlinks, since Windows 2000. A symbolic links is an alias for a directory in a different location, and to applications there is no difference between symbolic links and the actual directory. Unfortunately, there is no built-in tool for creating or managing these in Windows. There is, however, a free downloadable tool, called [Junction](http://technet.microsoft.com/en-us/sysinternals/bb896768.aspx).

So, here is what I did:

1. I created a folder in My Dropbox called “Apps”, and under that I created a folder called “My Weblog Posts”.
2. I moved all the content from “Documents\My Weblog Posts” to “My Dropbox\Apps\My Weblog Posts”.
3. I deleted “Documents\My Weblog Posts”.
4. I opened a command window, and executed the following command

``` plain
> junction.exe "C:\Users\Johan\Documents\My Weblog Posts"
    "C:\Users\Johan\Documents\My Dropbox\Apps\My Weblog Posts"
```

And voilà, I now have a symlink in my Documents folder, pointing to the folder in My Dropbox. Rinse, and repeat this on my laptop, and suddenly my drafts are available on both!

This is, of course, not only useable for Windows Live Writer, but for all applications that keep its data files in some unconfigurable folder somewhere, that you would like to have available on multiple computers.

Hmmm, maybe I should get back to writing that WF4 tutorial now…