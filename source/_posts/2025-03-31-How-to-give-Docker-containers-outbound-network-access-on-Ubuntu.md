---
title: How to give Docker containers outbound network access on Ubuntu
author: johan
date: 2025-03-31 16:51:39
tags: [docker, ubuntu, linux]
---

The other week I was setting up [FreshRSS](https://freshrss.org/) as a replacement for [Inoreader](https://www.inoreader.com) that I have been using for years.
Inoreader works fine, but like many other companies they are trying to integrate lots of AI crap nowadays, and I just down want that in an RSS aggregator! So I thought it was
time to try something self-hosted, and FreshRSS seemed to tick every box.

Anyway, this post is not about FreshRSS[^1], but rather how I solved a Docker problem that I'm sure I've run into several times before. I am writing these words in the
hope that next time, I'll remember to read my own blog instead of searching on the Interwebs for at least two hours...

I was going to install FreshRSS on a [Hetzner](https://www.hetzner.com/) virtual server running Ubuntu 22.04, and since that server runs some other stuff as well, I decided that it would be easiest
to run FreshRSS in a docker container. So I followed the instructions at [https://github.com/FreshRSS/FreshRSS/tree/edge/Docker#readme](https://github.com/FreshRSS/FreshRSS/tree/edge/Docker#readme).
Everything worked fine, I could even import all my feeds from Inoreader - except that my docker container could not download any RSS Feeds.

So yeah, it seems that by default Docker on Ubuntu[^2] does not allow containers to access the outside network at all. After following a lot of false leads I finally determined that
all you have to do is to add `"iptables": true` to `/etc/docker/daemon.json`. So after changing this, my daemon.json now looks like this:

```json
{
	"iptables": true,
	"log-driver": "json-file",
	"log-opts": {
			"max-size": "50m",
			"max-file": "3"
	}
}
```

I'm no expert on Linux networking, even though I've been using Linux since 1998 or so, and even run publicly available servers since 2000, so I'm not going to explain exactly why this is needed.
But from my understanding, settings iptables to true in the config files makes Docker set up the required iptables rules on startup, and that fixes the outbound connectivity.

Now all I need to do is remember this for next time. Well, fingers crossed...

[^1]: Although I will say that FreshRSS seems to work splendidly. I have been using it exclusively for about two weeks now, and it's really great! I strongly recommend it if you don't mind hosting your own RSS aggregator!
[^2]: This at least seams to be true for Ubuntu 22.04 and 24.04, I haven't verified it on anything else.
