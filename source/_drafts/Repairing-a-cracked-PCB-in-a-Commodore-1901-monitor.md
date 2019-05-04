---
title: Repairing a cracked PCB in a Commodore 1901 monitor
author: johan
date: 
tags: [retro computing]
---

This is the second and final part in a very short series where I improve and repair my Commodore 1901 monitor. In part 1 I added a SCART connector with analog RGB and audio support, but also discovered that the colours were a bit off, and discovered a crack on the PCB. In this part, I will repair the PCB and hopefully fix the colours.

First I had to have a good look at the crack:

{% asset_img PCB_on_crack.jpg %}

That doesn't look too fucking good. No less than nine (9) traces are broken. Fortunately, since this is an old monitor, the PCB is single layer, so there are no traces on the back, and no traces inside. The easiest way to repair a broken trace on a PCB is to find a solder joint on each side of the crack and solder a wire over the crack. But I also wanted to try another way. So for the first three traces, where there was enough space, I just scraped a bit of the outer layer off, and soldered a very short piece of wire right over the crack.

{% asset_img PCB_partially_repaired.jpg %}

For the rest of the broken traces, there just wasn't enough room to use this method, at least not with the tools and skill at my disposal. So I had to solder wires over the rest of the cracks.

{% asset_img PCB_fully_repaired.jpg "Looks like a nest of snakes... " %}
