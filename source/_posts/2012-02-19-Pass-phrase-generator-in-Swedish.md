---
title: "Pass phrase generator in Swedish"
extid: posts/9313
author: johan
date: 2012-02-19 15:49:20
updated: 2012-02-19 15:49:20
slug: Pass-phrase-generator-in-Swedish
tags: [passwords, security, swedish, projects]
---

Some time ago, the [XKCD](http://xkcd.com "The awesomest comic on the web. Really!") comic had a strip (below) about why pass phrases makes a hell of a lot better passwords than the standard passwords that many systems force us to use. I really liked the idea, and tried to use phrases instead. 

![](http://imgs.xkcd.com/comics/password_strength.png)

As it turns out though, it’s pretty hard to come up with random phrases on your own. They are not particularly random. Luckily, [Jeff Preshing](http://preshing.com/) felt the same, and created [passphra.se](http://passphra.se/ "At first I thought this was a Swedish site, due to the .se tld. But, no."), where you can generate random pass phrases in English, Spanish or French. So I’ve been using that for a while. 

But meanwhile, I was thinking “It would be neater to have this in Swedish”. And this weekend I finally managed to get away from doing other stuff long enough to create Lösenfras (Pass phrase).

First I had to find a suitable Swedish dictionary, that was free to use and downloadable. I found [Den Stora Svenska Ordlistan](http://www.dsso.se "The Great Swedish Dictionary?") with a [CC](http://creativecommons.org) license. I hacked together a [small utility to extract just the words in json format](https://bitbucket.org/nahojd/l-senfras "It was not test-driven. I used more of a hack-and-fix approach..."), and found that it contained about 88 000 words. Awesome! Four random words from that dictionary gives you 60 [quintillion](http://en.wikipedia.org/wiki/Quintillion "54 * 10^18") permutations. That should be secure enough! 

So then I created a small web page that loads all the words, and suggests four random words. Unfortunately, the phrases it suggested were not particularly easy to remember. How about <em title="drop-down menu on a daily basis left-wing natural economy">rullgardinsmeny dagligdags vänsterextremistisk naturahushållning *or <em title="exchange control pimplyness proletariat queer feminist">valutareglering finnighet proletariat queerfeministisk*? Yes, Swedish has a lot of compound words.

So after some fiddling around and a good suggestion from my illegitimate wife I decided to remove all personal named, words with other letters than a-z and åäö, and all words with more than two syllables, which in Swedish pretty much equals two vowels, since we don’t really have syllables with more than one vowel. That left me with about 24 000 words. Still a lot, gives 331 quadrillion permutations, and is gives a lot easier to remember phrases. 

So, there it is. [Lösenfras](/lösenfras). Enjoy. I know I will!

[![lösenfras](/images/Windows-Live-Writer/Pass-phrase-generator-in-Swedish_CF39/l%C3%B6senfras_thumb_1.png "lösenfras")](/images/Windows-Live-Writer/Pass-phrase-generator-in-Swedish_CF39/l%C3%B6senfras_4.png)

One might wonder why I would write a post about a Swedish pass phrase generator in English. Well, I just couldn’t be bothered to make the lang property of my html tag dynamic. And any non-Swedish speaking readers are welcome to use the pass phrase generator of course, although I believe the phrases will be just a tiny bit harder for you to memorize… 

Rånmord granhäck kod anslå.
<div style="margin: 0px; padding: 0px; float: none; display: inline;" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:8d7a6b39-4cbb-4340-992d-a7bb0a07d1de" class="wlWriterEditableSmartContent">Tags: [passwords](/tags/passwords), [security](/tags/security), [swedish](/tags/swedish), [projects](/tags/projects)</div></em></em>
