---
title: >-
  Setting up a Compact Flash card with Classic Workbench and WHDLoad for Amiga
  600/1200
author: johan
tags:
	- retro computing
categories:
	- retro computing
---

About a year ago, I bought an Amiga 600. It did not have a hard drive, but all Amiga 600 have an IDE port, and you can use a Compact Flash card with a CF-IDE adapter as a hard drive. That worked fine, and I could install Workbench on the CF card, and use it. But eventually I though I should try to use WHDLoad, so that I could run more games directly from the hard drive. I found a [video by Nostalgia Nerd on Youtube](https://youtu.be/1k1328geyI8), where he goes through the process of installing [Classic WB](http://classicwb.abime.net) and [WHDLoad](http://www.whdload.de/) on a Compact Flash card, unfortunately this video is (as of this date) three years old, and also very... quick? With the help of the comments[^1] and some googling, I managed to get it working, though.

Now, about a month ago I was given an Amiga 1200. This one actually had a hard drive, but who knows how long that will keep on working, so I though I'd replace it with a Compact Flash card as well. This time, however, for the benefits of my readers[^2] I thought I'd write down the process in a blog post. I thought it might be useful because a) I prefer written instructions, and b) it would give me a chance to update and correct the instructions so that they actually work.

The original video can be found [here](https://youtu.be/1k1328geyI8), along with [instructions and download links](http://www.nostalgianerd.com/whdload).

I won't go into the hardware side of this, because there's really nothing to it. But you will need a Compact Flash card with a capacity of at least 4GB, some kind of CF-IDE adapter and of course a Compact Flash reader for your computer. This tutorial also assumes that you are running Windows, although it should be possible to use pretty much the same procedure with [FS-UAE](https://fs-uae.net) on Linux or Mac.

## Step 1 - Download stuff

First of all, you will need to download a bunch of software.

### WinUAE - Amiga emulator

This tutorial uses the WinUAE amiga emulator, which can be found on [winuae.net](http://www.winuae.net). I was using version 4.0.1, although I now see that version 4.3.0 is available. From what I can tell, the difference seems to be very minor, so it shouldn't matter.

### Kickstart / Workbench

You will need a copy of the Kickstart ROM and Workbench disk images, version 3.0 or 3.1. This is still under copyright, and at least the Workbench images can be bought from [amigaforever.com](https://www.amigaforever.com/media/). They can also be found on several places on the Internet, as usual [DuckDuckGo](https://duckduckgo.com) is your friend.

### PFS3 File System support

In order to use large hard drives (or Compact Flash cards), you will need to download the handler for that from http://aminet.net/package/disk/misc/pfs3aio. This is not strictly necessary, but it's faster than the standard AFFS and supports larger partitions.

### Classic WB

Hard disk images for Classic WB can be found at (classicwb.abime.net)[http://classicwb.abime.net/]. I used the __LITE__ version for the Amiga 1200, but for an Amiga 600 you probably want the __68K__ version.

You will also need kickstart files for WHDLoad. These can be found for example at (paradise.untergrund.net)[https://paradise.untergrund.net/tmp/PREMIUM/amiga_tools/], it's the `kickstarts.lha` file you're looking for.

### Game and Demo packs

The original tutorial suggested that game packs could be downloaded from http://kg.whdownload.com/kgwhd/, but that doesn't seem to work anymore. I downloaded both games and demo packs from ftp://grandis.nu/Commodore_Amiga/Retroplay/, but they are available from many more places, just search for `whdload games pack`.

Put everything in a folder somewhere on your PC.

## Step 2 - Clean the CF card

In order to use the Compact Flash card in an Amiga, you need to remove all previous file system information from it. In order to do this, run `diskpart` in an Administrative command prompt.

First, list your disk by entering `list disk`. This should give you a result something like this:

{% asset_img diskpart-list-disk.png "Yes, I have many drives in my computer." %}

Next, select your compact flash card, in my case it's disk 6: `select disk 6`. _Be very, very sure that you select the correct disk. You will destroy everything on it._

{% asset_img diskpart-select-disk.png "Once, I did this on the wrong disk. Took me days to recover the data!" %}

If you want to be completely sure that you have selected the correct disk, you can run `detail disk` just to verify. When you are 100% sure, run `clean`. This completely wipes the file system information from the disk, making it ready to use in the Amiga. `exit` diskpart.

## Step 3 - Configure virtual Amiga in WinUAE

Start WinUAE as Administrator. Now we need to configure the system, and make it a little faster than a real Amiga. Otherwise this process will take literally[^3] forever.

### CPU and FPU

- CPU: 68040 (or maybe 68060) / JIT
- FPU: CPU Internal / More Compatible
- CPU Emulation speed: Fastest possible

{% asset_img winuae-cpu-and-fpu.png %}

### Chipset
Uncheck cycle exact, otherwise leave the default values.

{% asset_img winuae-chipset.png %}

### RAM

Add some Z3 Fast RAM, I used 64 MB.

{% asset_img winuae-ram.png %}

### ROM

Use the appropriate Kickstart ROM (probably the same as in your amiga). It needs to be version 3.0 or 3.1.

### Floppy drives

Add the Workbench installer disk image to DF0:. It should _not_ be write protected (use a copy if you don't want to risk modifying your original image).

### Hard drive (Compact Flash, really)

Now you need to add your compact flash card as a file system, as well as the pfs3 handler. Enter the CD & Hard drives section.

#### Add the pfs3aio archive as device f
- Add Directory or Archive
- Select Archive or plain file
- Select pfs3aio.lha
- Device name: f

{% asset_img winuae-add-pfs3aio-device.png %}

#### Add the Compact Flash card
- Add Hard Drive (this is where you need to be Administrator)
- Select the Compact Flash card as hard drive
- Change from UEA to IDE (Auto)
- Make sure Read/Write is checked
- Click Add hard drive

{% asset_img winuae-add-cf-as-hdd.png %}

Start the virtual machine!

## Step 3 - Partition Compact Flash card
- Open the Install disk and HDTools
- Start HDToolbox
  - You should see Interface SCSI, Address 0, LUN 0, Status Unknown
- Click "Change drive type" -> "Define new" -> "Read configuration" -> "Continue" to configure the CF drive (ignore the values read, the Amiga does not really understand 4 GB Drive)
- Click OK and go back to the list of hard drives in the system.
- Click "Partition Drive"
- Set up a small(ish) system partition, like 250 MB. Change the name to DH0.
- Set up the rest of the CF Card as a partition, name it DH1.
- Optional: Use the pfs3 file system
  - Click Advanced options -> Add/Update
  - Click Add New File System
    - Enter filename `f:pfs3aio` (__NOT__ pfs3_aio-handler) and click OK
    - Change DosType to `0x50465303` and remeber to press Enter in the field
    - Click OK and OK to get back to your partitions
  - Click Change to change to the new file system
    - Select Custom File System or PFS/03 (depending on your Workbench version, I think)
    - Make sure Identifier says `0x50465303` (otherwise change it)
    - Change MaxTransfer to `0x1fe00` (and press enter)
    - Click OK
  - Repeat last step for the other partition
  - Click OK
  - Click Save changes to drive (if you get an error here, you may want to try another Card Reader)
- Exit HDToolbox and reboot the virtual Amiga.

## Step 3 - Install Classic WB
- First, format the partitions by right clicking on them and select Icons -> Format disk from the menu. Name DH0 System and DH1 whatever you want. Make sure to use Quick Format. Confirm all warnings.
- Press F12 to enter the WinUAE settings and go to CD & Hard Drives.
- Now you need to add the `System.hdf` file that you extracted from the Classic WB archive you downloaded in Step 1. Click Add Hardfile and select the System.hdf file. Make sure that the HD Controller is UAE, and name the device DH2. You should set boot prio to 1 (not 0).
- You can remove the pfs3aio device.
- Go to Floppy drives and eject all floppy drives
- Restart the virtual machine. It should now boot into the Classic WB installer.
- Follow the instructions, and when prompted to insert a Workbench disk, press F12 to enter settings and do that.
- After the installation is done, and you have restarted, you probably will not see you compact flash partitions. This is because the Amiga gets confused by the 2 System partions. Rename the Classic WB partition to System2 (or something) and restart the virtual machine. You should now see all partitions.
- Start DOPUS by clicking RUN and selecting DOPUS.
- Copy all files from DH2 to DH0 (if DH2 does not appear in the list, you may have to type it in)

## Step 4 - Copy Games and Demos for WHDLoad
- First, we need to mount the folder where you put your games, demos and kickstarts as a file system in the virtual amiga.
  - Go into WinUAE settings -> CD & Hard Drives and click "Add Directory or Archive".
  - Click "Select Directory" and point to where your Games and Demos are.
  - Put `PC` as both Device name and Volume label. Uncheck bootable. Click OK, and reset the machine.
- Then, we need to copy all the kickstart files. 
  - If you haven't done so already, unpack the kickstarts.lha archive into a folder.
  - Open DOPUS again, and select PC for the left side, and navigate into where you unpacked your kickstarts.
  - Copy all the kickstarts file to `DH0:Devs/Kickstarts`. Overwrite any files already there.
- The Games and Demos need to be unpack into individual folders grouped by initial. For example `Games/A/AnotherWorld_v2.4_0425`. For games beginning with a number, the folder should be called `0_9`. This can be done on the PC, or you can unpack them using DOPUS (as long as you have grouped them by initial).
- Use DOPUS again to copy the files from PC to DH1. If you did not unpack the archives earlier you can use `Arc Ext` to extract all the archives, buy you will have to do it folder by folder. I copied them to `DH1:Games`and `DH1:Demos`, but you can organise your files however you want.
- Go back into settings, and remove all file systems except for the Compact Flash card. Reset the system, and it should boot back into Classic WB, but this time from you Compact Flash card.
- Right click the top bar and select Workbench -> Startup from the drop down menu. Click Assign.
- Change the locations for Games (and Demos) to where you put them. In my case, change the line that reads `Assign >NIL: A-Games: SYS:Games` to `Assign >NIL: A-Games: DH1:Games` (and likewise for demos).
- Click the close icon in the top left corner and then click Save. Reset the machine again.
- Now we need to add the games (and demos) to WHDLoad. Double click on the Files drawer in the bottom, and select AddGames. This may take some time. Do the same for AddDemos.
- Now you can verify that the games are available. Right click on the desktop (of the Amiga!) and select RUN -> Games. This should bring up the GamesMenu where you now should see a long list of games.

## Step 5 - Hardware install
There is not really much to this, and the video explains it pretty good. Use a CF-IDE adapter of some kind, and connect it to the IDE port of the Amiga. That's it.

Start the Amiga (the real one), and it should boot to your Compact Flash card. Bring up the RUN -> Games menu, and double click a game to start it!

[^1]: Youtube comments are more useful than their reputation would have you believe!
[^2]: That is, me, a few months from now.
[^3]: figuratively