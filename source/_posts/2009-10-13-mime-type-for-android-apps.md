---
title: "Mime type for Android apps"
author: johan
date: 2009-10-13 22:14:44
updated: 2009-10-13 22:14:44
slug: mime-type-for-android-apps
tags: [android]
---

I recently got an [Android](http://www.android.com/) phone (a [HTC Hero](http://www.htc.com/hero), to be specific), and of course I felt the need to develop applications for it. I have a few in mind, and I’m sure I will dwell on that in upcoming blog posts. One thing, however, I learned immediately: In order to get the phone to install your app from a web server, you need to have the correct [mime type](http://en.wikipedia.org/wiki/Mime_type) for it.

It turns out that the correct mime type for an .apk file is

`application/vnd.android.package-archive`

You also need to change the settings to allow installation of programs that doesn’t come from [Android Market](http://www.android.com/market/). This setting is found in* Settings > Program*, and is probably called “*Unknown sources*” in English (it is definitely called “Okända källor” in Swedish).