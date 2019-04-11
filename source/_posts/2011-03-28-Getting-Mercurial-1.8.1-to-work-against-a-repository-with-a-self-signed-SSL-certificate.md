---
title: "Getting Mercurial 1.8.1 to work against a repository with a self-signed SSL certificate"
extid: posts/2049
author: johan
date: 2011-03-28 21:10:08
updated: 2011-04-04 00:54:26
slug: Getting-Mercurial-1.8.1-to-work-against-a-repository-with-a-self-signed-SSL-certificate
tags: [mercurial]
---

For a project with some friends, we’ve set up our own private [Mercurial](http://mercurial.selenic.com) repository. We’re publishing it using [hgweb and apache](http://mercurial.selenic.com/wiki/PublishingRepositories), and since we want it encrypted, we use a self-signed certificate. This has been working fine until recently, when I upgraded Mercurial from 1.6.x to 1.8.1, because it turns out that in Mercurial 1.7.2, they started throwing a big fat error when you try to work against a repository with a self-signed certificate:


``` plain 
C:\Users\Johan\HgReps\MsmqRestService>hg in
abort: error: _ssl.c:490: error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICAT
E:certificate verify failed

```



There is [a long thread at selenic.com discussing why you can’t push/pull to a https server with a self-signed certificate](http://selenic.com/pipermail/mercurial/2011-January/036561.html). But to save you (and myself) from having to read it (again), I thought I’d jot down the steps to get it working again.

1.  Open your repository in a web browser. I used Firefox 4.
2.  Save the certificate. In Firefox by clicking the domain name, more information, view certificate, details tab, export certificate. I’m sure you can figure out how to do it in another web browser as well. Be sure to select *X.509 Certificate (PEM)*, when exporting.  
[![A totally unnecessary picture showing how to display the certificate in Firefox 4. But the post looks much nicer with an image in it!](/images/Windows-Live-Writer/Getting-Mercurial-1.8.1_12409/capture_thumb.png "A totally unnecessary picture showing how to display the certificate in Firefox 4. But the post looks much nicer with an image in it!")](/images/Windows-Live-Writer/Getting-Mercurial-1.8.1_12409/capture_2.png)
3.  Edit the `cacert.pem` file for TortoiseHg (if that’s the way you installed mercurial).   
On my machine, the full path is `C:\Program Files\TortoiseHg\hgrc.d\cacert.pem`. 
4.  Copy the contents from your exported certificate file, and paste them to the bottom of cacert.pem
5.  Rejoice!  
<div style="margin: 0px; padding: 0px; float: none; display: inline;" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:a2c0d11c-d6df-43b6-ad5f-d68e2a4436a6" class="wlWriterEditableSmartContent">Tags: [mercurial](/tags/mercurial)</div>
