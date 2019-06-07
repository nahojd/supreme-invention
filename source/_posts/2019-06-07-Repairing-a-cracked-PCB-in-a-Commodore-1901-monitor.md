---
title: Repairing a cracked PCB in a Commodore 1901 monitor
tags:
  - retro computing
author: johan
categories:
  - retro computing
date: 2019-06-07 18:49:33
---

This is the second and final part in a very short series where I improve and repair my Commodore 1901 monitor. In {% post_link Adding-an-RGB-SCART-connector-to-a-Commodore-1901-monitor 'part 1' %} I added a SCART connector with analog RGB and audio support, but also discovered that the colours were a bit off -- especially when using an RGBi input, such as CGA -- and discovered a crack on the PCB. In this part, I will repair the PCB and hopefully fix the colours.

First I had to have a good look at the crack. It was in the lower left corner of the PCB, close to the potentiometers that adjust the color levels, as marked in the picture below.

{% asset_img Commodore_1901_monitor_full_backside_nudity.jpg 'Why is there a space in the middle of the of??' %}

After pulling the board out a bit and turning the monitor upside down, I could get a closer look at the crack.

{% asset_img PCB_on_crack.jpg %}

That doesn't look too fucking good. No less than nine (9) traces are broken. Fortunately, since this is an old monitor, the PCB is single layer, so there are no traces on the back, and no traces inside. The easiest way to repair a broken trace on a PCB is to find a solder joint on each side of the crack and solder a wire over the crack. But I also wanted to try another way. So for the first three traces, where there was enough space, I just scraped a bit of the outer layer off, and soldered a very short piece of wire right over the crack.

{% asset_img PCB_partially_repaired.jpg %}

For the rest of the broken traces, there just wasn't enough room to use this method, at least not with the tools and skill at my disposal. So I had to solder wires over the rest of the cracks.

{% asset_img PCB_fully_repaired.jpg "Looks like a nest of snakes... " %}

After this I reassembled the monitor (well, actually, I finished {% post_link Adding-an-RGB-SCART-connector-to-a-Commodore-1901-monitor 'the SCART mod' %} as well) and connected my Bondwell Model 8 to the RGBi-input. To my great surprise everything worked perfectly! The lovely CGA palette of white, cyan and magenta was as vibrant as ever with no sign of the yellowish tint from before, and some careful banging on the side of the screen no longer causes the colors to change. So I have to label this a complete success!

I now have the perfect monitor for my small[^1] collection of retro computers. It takes RGBi, SCART with analog RGB and separate Chroma and Luma input (like S-VIDEO). And it even has a built-in speaker! The only input I so far haven't had much success with is composite. If I connect composite to the Luma input (the yellow RCA jack), I get a monochrome picture (not a great surprise). If I connect it to the Chroma instead, I get no picture at all. If I split the composite cable and connect it to both, I still only get monochrome. If anyone has a working way to connect a composite signal to separate luma and chroma inputs, I would be very interested. A minor annoyance though, as I can connect composite to a TV instead. So, yay, working Commodore 1901 monitor!

Finally, here is a picture of my five year old son playing Krakout on the repaired monitor!

{% asset_img boy_playing_krakout_ii_on_c64_and_commodore_1901.jpg "Some games never get old! Unfortunately, the improved colours does not show at all in this image..." %}

[^1]: I would consider my collection small. There are others in my family who would voice a different opinion...