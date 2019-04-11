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
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:9e5398a7-d55a-4942-9587-e3f0e92e5d92" class="wlWriterEditableSmartContent">Tags: [android](/tags/android)</div>
