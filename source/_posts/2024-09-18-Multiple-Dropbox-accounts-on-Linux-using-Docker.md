---
title: Multiple Dropbox accounts on Linux using Docker
author: johan
date: 2024-09-18 16:13:42
tags: [dropbox, linux, docker]
---

I use Dropbox heavily, both for personal files, and for work files. So I have a personal account, and I also have a work account. I am also what you
might call an Operating System Agnostic, that is I use Windows, Mac OS and Linux pretty much equally.

Now, for some reason the [Dropbox client for Linux](https://www.dropbox.com/install-linux) doesn't have support for multiple accounts at all. In fact, the Linux Dropbox client seems all but
forgotten. This is obviously a problem for me. Now, this isn't new to any one I suppose, but I think I figured out a pretty good way of handling
it, so that once it's set up I can just forget about it.

My method is to use the standard client for my personal account and Docker for my work account (vice versa would of course work equally well).
I use the [Dropbox Docker Image](https://github.com/otherguy/docker-dropbox) created by otherguy. There might very
well be other dropbox images that work better, I have no idea, but this one does the job for me.

I recently had to set this up for maybe the third time, so I figured it was time to document it, hence this post.

First, I created a docker compose file, `compose.yaml`:

```yaml
services:
  dropbox:
    image: otherguy/dropbox:latest
    container_name: dropbox
    volumes:
      - /home/johan/.dropbox-approach:/opt/dropbox
    environment:
      TZ: "Europe/Stockholm"
      DROPBOX_UID: 1000
      DROPBOX_GID: 1000
      POLLING_INTERVAL: 20
    restart: unless-stopped

```

The `DROPBOX_UID`and `DROPBOX_GID` should match the userid and groupid of your user, but in most single-user linux systems it will be __1000__, and you should of course use the appropriate time zone for your location. Other than that, the only interesting thing is where to map the volume to. Since the `/opt/dropbox` volume of this container not only contains your actual dropbox, but a number of other files as well, I found it to be best to map it to a hidden folder, and the symlink my actual Dropbox folder later.

After this, start the container, and start following the logs:

```bash
> dropbox compose up -d
> dropbox logs -f dropbox
```

The reason why you have to follow the logs is that the container will print a URL that you have to use to connect it to your dropbox account. It will look something like this:

```text
This computer isn't linked to any Dropbox account ...
Please visit https://www.dropbox.com/cli_link_nonce?nonce=768d09d7-c6b9-4574-8aad-7a8d41fa9728
```

Just paste this link into your [favourite web browser](https://www.mozilla.org/en-US/firefox/), log in to Dropbox and just like that, the syncing will start.

If you want to exclude some folders from syncing, you have to use the command line inside the container. For an overview of all the commands, use

```bash
> sudo docker exec -it dropbox gosu dropbox dropbox exclude help
```

To exclude a folder, use

```bash
> sudo docker exec -it dropbox gosu dropbox dropbox exclude add "Approach Dropbox/Top folder/Sub folder"
```

You have to start with the name of your dropbox, I have no idea why, but it's the same as the folder name in the file system (directly below the hidden folder, in my case .dropbox-approach).

The only thing remaining is to symlink it in your home folder:

```bash
> ln -s ~/.dropbox-approach/Approach\ Dropbox ~/Approach\ Dropbox
```

Now maybe I won't have to look allt this up a fourth time!
