---
title: "I’m gonna build my own blog engine, because it’s never been done before, or maybe I can just do it better!"
author: johan
date: 2010-10-18 23:06:23
updated: 2010-10-18 23:10:02
slug: irsquom-gonna-build-my-own-blog-engine-because-itrsquos-never
tags: [asp.net mvc, razor, ravendb, mercurial, blogging]
---

Today is the first day of my paternity leave, I’ll be home with my little daughter for at least six months. Which is great! But I immediately started to get the itch to write some code. I needed a hobby project. Since I suck at coming up with great ideas, I decided to [be a good Jedi](http://blog.wekeroad.com/blog/be-a-good-jedi-build-your-own-blog/) and build my own blog engine!

Yes, I know, there really is no need for another blog engine. Except that there is. The one I would like to use doesn’t seem to be out there. Also, it seems like a perfect way to try out some new technology that I’ve been wanting to use, without a client (or a manager) having opinions about it. And since I replaced the native comments of [Subtext](http://subtextproject.com/) with [Disqus](http://disqus.com/) a few weeks ago, I realized that there really aren’t that many features needed for a blog anymore.

So, my plan right now is to build it on [ASP.NET MVC 3](http://www.asp.net/mvc/mvc3), with the [Razor view engine](http://weblogs.asp.net/scottgu/archive/2010/07/02/introducing-razor.aspx). I’ve been wanting to try this whole [NoSQL](http://en.wikipedia.org/wiki/NoSQL)-thing for a while, so I’ll use [RavenDB](http://ravendb.net/) for persistence. I’ve also been wanting to try a distributed version control system (getting reeaaally [tired of TFS](http://www.nearinfinity.com/blogs/joe_ferner/why_i_dislike_tfs_-_team_found.html)!), so I’ll use [Mercurial](http://mercurial.selenic.com/) and host it on [bitbucket](http://bitbucket.org/). I’m sure I’ll think of other sweet stuff to use as well, and maybe I’ll change my mind on some of these along the way. 

But now it’s on it’s way. I called the project “Alpha”, because that’s what it’ll always be. But I aim to replace Subtext with Alpha before the end of this year. And hopefully the process of building it will give me reasons to write some blog posts, since I will not be writing blog posts about the process of taking care of my daughter. 

Anyway, the project is available at [https://bitbucket.org/nahojd/alpha/overview](https://bitbucket.org/nahojd/alpha/overview). Not much to see yet, though.
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:ed2bffbe-a323-4f35-bb83-ef5076f37e52" class="wlWriterEditableSmartContent">Tags: [asp.net mvc](/tags/asp.net%20mvc), [razor](/tags/razor), [ravendb](/tags/ravendb), [mercurial](/tags/mercurial), [blogging](/tags/blogging)</div>
