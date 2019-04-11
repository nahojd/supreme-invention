---
title: "Debugging Silverlight needs IE?"
author: johan
date: 2010-04-23 21:24:57
updated: 2010-04-23 21:24:57
slug: debugging-silverlight-needs-ie
tags: [silverlight, visual studio, debugging, internet explorer]
---

I’ve decided to learn at least a little [Silverlight](http://www.silverlight.net/), since it seems to be pretty cool, and I feel the need to learn something new (yeah, yeah, I’ll learn [Erlang](http://www.erlang.org/) or something equally hardcore next). Since I basically don’t know any Silverlight at all, I though I’d start by following a tutorial of some kind. And since [the Silverlight Training Course for Silverlight 4 on Channel 9](http://channel9.msdn.com/learn/courses/Silverlight4/) was announced pretty much the same day, I chose that one.

In [Lab 3](http://channel9.msdn.com/learn/courses/Silverlight4/SL4BusinessModule3/SL4LOB_03_User_Registration/), I had some difficulties (probably due to my inability to press the correct keys on the keyboard), and felt that I needed to set a few breakpoints and debug. So I did, and pressed F5. But unfortunately, my breakpoints were not hit. Instead I got the always equally entertaining message

``` plain
The breakpoint will not currently be hit. No symbols have been loaded for this document.
```

Ok, no biggie, we’ve all seen this before. Remove all bin and obj folders, clean, rebuild, shift-F5 to force reload in the browser – but no, still nothing.

However, one thing struck me. I had noticed that my default browser, Firefox, was bad at loading the latest version of the Silverlight app when I started debugging. So I just pasted the URL into Internet Explorer instead and, if necessary, pressed Shift-F5 to force a reload. But now I tried to actually set IE as the default browser (which is insane, I know), and lo and behold - debugging works!

So I draw the conclusion that you need to have IE as the default browser to get Silverlight debugging to work. If so, it should probably be pointed out somewhere, since pretty much every web developer has Firefox or Chrome as the default browser (can’t live without Firebug)...

A little more experimentation has led me to believe that IE does not need to be the default browser of the system, it just needs to be the default browser for the debug start page. If I right-clicked on the start page in the Web project and clicked "Browse with...", and set IE as default, debugging works. If I set Firefox or Chrome as default, debugging doesn't work, even if I open the page manually in IE.

I have yet to find out if this is by design or by accident…