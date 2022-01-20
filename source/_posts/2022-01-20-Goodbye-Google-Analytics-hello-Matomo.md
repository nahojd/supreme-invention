---
title: 'Goodbye, Google Analytics, hello Matomo!'
author: johan
date: 2022-01-20 19:58:23
tags:
	- analytics
	- meta
---

This blog has been using [Google Analytics](https://analytics.google.com) (GA) to track visitors[^1] since pretty much the beginning. Mostly because it's free, and I knew how to use it since I've been using it professionally for clients since forever. But I haven't really felt good about it for some time, for two primary reasons:

1. I see no reason whatsoever in providing Google with data about my visitors.
2. It's really not that good anymore. GA is very focused on e-commerce, and simple not suited for this task anymore.

So when I read about the recent [Schrems II ruling in Austria](https://www.jurist.org/news/2022/01/austria-data-authority-rules-websites-use-of-google-analytics-violates-gdpr/), where they find that Google Analytics is now probably[^2] illegal i the EU, I thought it was time for a change! The only question then, is what to use instead!

One obvious option was to simply remove the GA tracking script and not replace it at all, instead just relying in the server logs. To be honest, that would probably be enough for my purposes, even if I would lose some information about browser capabilities, screen sizes and such. But there are other sites where I also would like to replace GA, where that wouldn't be enough, so I though I'd better find a better alternative.

There are, of course, numerous alternatives, at least if you're willing to pay. If I was doing this for a client, I wouldn't have a problem with that. But for this blog, where I write the occasional post and definitely don't make any money, I'd prefer it if it was free. I also wouldn't mind hosting the tracking service myself, as that would pretty much guarantee that the data doesn't fall into the wrong hands.

So after a bit of research, I decided on [Matomo Analytics](https://matomo.org/), which can be installed on-premise, but also has a cloud hosting solution. It also has a lot of plugins, and even lets me import my old data from Google Analytics!

Matomo has pretty good documentation, with [nice guide on how to install it on a server](https://matomo.org/docs/installation/), but I decided to use a Docker container instead, since there is an [official image from Matomo on docker hub](https://hub.docker.com/_/matomo). Matomo also needs a MySQL or MariaDB database to store the data in, and to be honest I had some problems getting Matomo in the container to talk to the database.

In the end I installed MariaDB from [the official docker image](https://hub.docker.com/_/mariadb/) as well, as it seemed easier to get the Matomo container talking to the database in another container, than to the db on the host system.

If it is of any help to someone else trying this, I installed the images like this, although you should probably create a persistent volume for mariadb as well, if you have to reinstall it:

```bash
$ sudo docker run -d --name mariadb --env MARIADB_ROOT_PASSWORD=somesupersecretpassword mariadb:latest
$ sudo docker run -d -p 8080:80 --link mariadb:db -v matomo:/var/www/html --name matomo matomo
```

I then inspected the mariadb container to find out what IP address it was using (it was `172.17.0.3`!), and used that when connecting from Matomo. Oh yeah, I also attached to bash in the mariadb container and created and database and a database user for Matomo to use before setting up Matomo.

And that, I think, was it! Now it's just a matter of seeing if it works properly!


[^1]: Nothing nefarious, I swear! I just want to be able to see which posts are getting read, what browsers and operations systems you are using and that stuff.
[^2]: When it comes to law, EU and GDPR, it is always probably...
