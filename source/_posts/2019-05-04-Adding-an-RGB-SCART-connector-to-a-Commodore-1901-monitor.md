---
title: Adding an RGB SCART connector to a Commodore 1901 monitor
tags:
  - retro computing
author: johan
date: 2019-05-04 23:21:58
---


So, this post is going to be a departure from most of the previous content on this blog. This may or may not be indicative of future posts.

A couple of months ago, I bought a cheap, used Commodore 1901 monitor[^1] from Tradera. The Commodore 1901 has digital RGBi input using D-SUB 9 connector, as well as separate Luminance and Chrominance inputs via RCA jacks (which is the same signal as S-Video, just different connectors). I thought this would be a good monitor for my old Bondwell Model 8 computer, which only has CGA output (and probably deserves a post of its own). It would probably also work with my Commodore 64. The Commodore 1901 also has a built-in speaker, that connects with yet another RCA plug, so I wouldn't even need a separate speaker.

When I connected my Bondwell to the monitor, it was indeed glorious, as evident in the image below. What is harder to see is that the colours where a bit off, there was a bit of a yellow tint that kind of came and went.

{% asset_img Commodore_1901_Showing_CGA_Image_From_Bondwell_Model_8.jpg "The CGA graphics look good, right? It seems my Test Drive skills has deteriorated a bit since I was a kid, though..." %}

I managed to find {% asset_link Service_Manual_Commodore_Video_Monitor_1901_High_Quality.pdf "the service manual for the Commodore 1901 monitor" %}, and found that there were a couple of potentiometers that could be adjusted if the colour was off. So I opened the monitor and adjusted the potentiometers which at least made the colour a little bit better. Unfortunately, I also noticed that the PCB had a small crack, which caused some bad connections, and was probably the cause of the colour problem. More about this later.

But what I also found, to my great surprise, was a number of solder points that looked like they could fit a SCART connector, and a matching hole in the metal backplate. What on earth could this be for? Maybe this monitor came in a different version[^2], with a SCART connector? But if so, what kind of signals was used[^3]? And did this version actually use those signals? Would it be possible to get analog RGB input by adding a SCART connector?

{% asset_img Solder-points-on-PCB.jpg "The hole that has no solder in it is not connected to anything. According to Wikipedia it is pin 8, which controls Status & Aspect Ratio up." %}

A bit of research indicated that yes, this might indeed be possible. I found [a thread on amibay.com](http://www.amibay.com/archive/index.php/t-62132.html) and [blog post by a Danish guy](http://elgensrepairs.blogspot.com/2012/12/finding-repairing-and-enhancing.html) (unfortunately missing all images[^4]) that discussed this. The problem seemed to be that the solder points for the SCART connector on the PCD are oriented backwards, so that a standard 90-degree connector won't fit. So the usual solution seemed to have been to solder wires between the PCB and the SCART plug. However, I managed to find an [angled SCART connector on eBay](https://www.ebay.com/itm/90-RIGHT-ANGLED-FULL-21-PIN-SCART-SOCKET-CONNECTOR-BUS-PCB-HORIZONTAL-MOUNTING/161119318901?epid=709438006&hash=item258375b375:g:8AkAAOSw5UZY8~VE) that seemed to be oriented the other way around. It sure looked like it would fit!

So, the first thing to do was to remove the solder blocking the holes. Time to heat up my solder sucker!

{% asset_img Solder-points-on-PCB-after-removing-old-solder.jpg "Time to heat up my solder, sucker! " %}

After this, it was simply a matter of fitting the SCART connector and soldering it in place. Or rather, it would have if the darn plug would have fitted through the hole in the metal frame!! When I had fitted the legs through the holes in the PCB, it was completely impossible to get the plug through the hole. In the end, I hade to bring out a knife and go to town on the poor plug.

{% asset_img Mangled_SCART_Connector.jpg "This SCART connector has seen better days. " %}

Finally, I was able to fit the SCART connector through the hole, and solder it in place.

{% asset_img SCART_Connector_in_place.jpg "The SCART cable fits! " %}

And now, the moment of truth. Would this work? I have an Amiga 600 with a SCART cable that carries not only analog RGB video, but also sound. So maybe I would get sound through the built-in speaker as well? Time to connect a cable. Would it even fit the mangled SCART connector?

{% asset_img SCART_cable_connected.jpg "The colours on the RCA jacks are really confusing. The red and white are not stereo sound, and the yellow one is not composite video. I think that the white one is audio, yellow is luma and red is chroma. " %}

The answer to the last question is yes, it fits. And the answer to rest of the questions are yes, everythings works perfectly! I get a crystal clear image from the amiga, and I get the sound through the speaker! The only thing left to do was to make a hole in the plastic cover as well, which was easy since there was already an indication in the cover where to cut. Here is a picture of the Amiga workbench on the Commodore 1901 monitor:

{% asset_img Amiga_Workbench.jpg "Wow, nice moiré patterns! " %}

So hooray, everything is great! Except for the crack in the PCB, remember? Since I had the monitor open, and the soldering iron out, I decided to see if I could fix that as well. But I believe this post is long enough already, so that will have to wait until part 2.

[^1]: The Commodore 1901 monitor was a PAL-only monitor produced between 1986 and 1988, and was meant to be used together with the Commodore 128. It is not as famous as the 1084 monitor but, as we will see, with the SCART modification it is just as useful!

[^2]: The monitor was actually manufactured by Thompson. And Thompson did release their own version of it with a SCART connector, the Thompson 450G. Why the Commodore version came without it, I do not know.

[^3]: The SCART connector actually carries a lot of different signals. It can carry composite video, s-video and RGB, and event YPbPr, as well as stereo sound. Wikipedia has a [good article](https://en.wikipedia.org/wiki/SCART).

[^4]: While writing this post I checked the blog post again, and now it seems all images are back! This would have made it easier for me when I actually was working on the monitor!