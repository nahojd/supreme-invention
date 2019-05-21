---
title: The blog is now even more static than before!
author: johan
date: 2019-04-19 16:58:00
tags: [blogging, hexo]
---
This blog has been rather static lately. In fact, I haven't written a new post since 2014. I'm sure that has nothing to do with the fact that I became a father for the second time in 2014. Obviously a coincidence.

But now it's time for the blog to become static in a whole new way! For more than 8 years, this blog has been [running on my own blog engine](/posts/Up-and-running-on-the-Alpha-Blog-Engine!), but now the time has come to leave that behind, and move to the wonderful world of static site generators. I know, I'm a couple of years late too the party, but better late than never!

After looking at a lot of alternatives, and sinking way to many hours into making a combination of [gulp](https://gulpjs.com) and [metalsmith](https://metalsmith.io) work the way I wanted it to, I finally decided on using [Hexo](https://hexo.io) to generate the site. That worked pretty much out to box, although I still feel I have enough flexibiliy to make it work that way I want. Hexo generates a completely static site, and I just need a lightly configured [Nginx](https://www.nginx.com) server in front of it, mainly to keep some old links alive.

So now the source code for both the posts and the scaffolding for the blog lives on [Github](https://github.com/nahojd/supreme-invention), and publishing a new post is a simple matter of writing it in markdown and pushing to Github.

Or, well, it will be once I finish the script that auto publishes the blog. So, any day now.

I also gave the blog a new coat of paint, which was sorely needed. Hopefully this will lower the bar for me to write new posts, which _might_ mean that I will be able to produce more than one every five years. Fingers crossed.