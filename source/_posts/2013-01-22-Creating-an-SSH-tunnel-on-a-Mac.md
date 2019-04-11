---
title: "Creating an SSH tunnel on a Mac"
extid: posts/9409
author: johan
date: 2013-01-22 16:47:29
updated: 2013-01-22 16:47:53
slug: Creating-an-SSH-tunnel-on-a-Mac
tags: [ssh, mac, networking]
---

A while ago I wrote a post about [how to set up an SSH tunnel to access remote desktop](/posts/Setting-up-an-SSH-tunnel-to-access-Remote-Desktop-using-Putty-and-SSHD-on-Linux). Since then I've started using a Macbook Air, and naturally I want to do the same.

As it turns out, however, I'm terrible at remembering the syntax. So here it is, for my own future reference:

I want to access Remote Desktop on RDPSERVER, port 3389, and map it to port 3391 on my local machine. To to this I will connect to SSHSERVER and tunnel through that.

`> ssh -l username -L localhost:3391:RDPSERVER:3389 SSHSERVER`

Easy, peasy. Now I will never forget it again!