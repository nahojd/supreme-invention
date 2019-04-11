---
title: "Fixing the blog preview theme in Windows Live Writer"
extid: posts/9281
author: johan
date: 2012-02-16 09:00:59
updated: 2012-02-16 09:00:59
slug: Fixing-the-blog-preview-theme-in-Windows-Live-Writer
tags: [blogging, windows live writer]
---

I’ve been using [Windows Live Writer](http://explore.live.com/windows-live-essentials-other-programs?T1=t5) to write my blog post ever since I started blogging. Even though the 2011 update made it a little worse than before, it’s still a pretty nice tool to write your posts. Especially with the [PreCode plugin](http://precode.codeplex.com/) that allows me to write code with support for [SyntaxHighlighter](http://alexgorbatchev.com/SyntaxHighlighter/).

However, since I changed the theme of my blog recently, something went awry with the preview theme in Live Writer. More specifically, the white background for the blog posts disappeared, so it looked like this:

[![before](/images/Windows-Live-Writer/9d9296b0015a_7A42/before_thumb.png "before")](/images/Windows-Live-Writer/9d9296b0015a_7A42/before_2.png)

You’ll have to excuse the GUI being in Swedish, the computer I took the screenshot on came preinstalled with Swedish, and I although I’ve changed the system language to English, I’ve yet to find a way to change the language in Live Writer… Anyway, as you can see, it’s not optimal if you want to see what you’re writing.

I suppose I could change the Html of my blog, but there’s really nothing wrong with it, I would rather not change it just because Live Writer can’t interpret it correctly. Luckily, there’s another way. The theme files that Live Writer generates gets stored in `C:\Users\{username}\AppData\Roaming\Windows Live Writer\blogtemplates`. In this folder, there are some more folders with Guid-names. You just have to figure out which one is the correct one. In my case it’s easy, since I just have one blog setup.

Inside the Guid-named folder, you’ll find and index.html file, along with some images and other stuff. When I opened the index.htm file, it looked like this:

``` xml
<!DOCTYPE html><!-- saved from url=(0025)/ -->

<HTML lang="en"><HEAD><TITLE>All posts -- Johan Driessen's blog</TITLE>
<META charset="utf-8">
<META content="IE=edge,chrome=1" http-equiv="X-UA-Compatible">
<META name="viewport" content="width=device-width, initial-scale=1.0"><LINK title="Johan Driessen's blog (RSS)" rel="alternate" type="application/rss+xml" href="/feed/rss/"><LINK title="Johan Driessen's blog (Atom)" rel="alternate" type="application/atom+xml" href="/feed/atom/"><LINK title="RSD" rel="EditURI" type="application/rsd+xml" href="/rsd.xml"><LINK rel="icon" type="image/gif" href="/favicon.ico"><LINK rel="stylesheet" type="text/css" href="file:///C:/Users/jdr1/AppData/Roaming/Windows Live Writer/blogtemplates/3bd255a5-c119-4680-b880-561c5d8efdbd/8e466ec1-3cf5-4403-8f5c-96137b8aa6d3/combined_92C867FD2A8F31A0C.css"><LINK rel="stylesheet" href="file:///C:/Users/jdr1/AppData/Roaming/Windows Live Writer/blogtemplates/3bd255a5-c119-4680-b880-561c5d8efdbd/8e466ec1-3cf5-4403-8f5c-96137b8aa6d3/SmallScreen.css">

<meta http-equiv="CONTENT-TYPE" content="text/html; utf-8"></HEAD>
<BODY>
<DIV id="container"><H1><A href="/">{post-title}</A></H1>{post-body}</DIV></BODY></HTML>
```

Aha, lots of elements missing around the post. So I changed the important part to this:

``` xml
<DIV id="container">
<section id="main">
    <article class="post">
        <H1><A href="/">{post-title}</A></H1>
        <section class="content">
            {post-body}
        </section>
    </article>
</section>
</DIV>
```

And after restarting Windows Live Writer, it looks like this instead:

[![after](/images/Windows-Live-Writer/9d9296b0015a_7A42/after_thumb.png "after")](/images/Windows-Live-Writer/9d9296b0015a_7A42/after_2.png)

Changing the html code in index.htm fixes the appearance of the Edit mode. In order to fix the appearance of the Preview mode, I had to change the index[1].htm file as well, but I just pasted the same html there, and everything looked as it should!