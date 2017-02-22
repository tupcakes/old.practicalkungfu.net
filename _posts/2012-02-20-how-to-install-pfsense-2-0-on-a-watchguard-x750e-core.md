---
id: 62
title: '>How to install pfsense 2.0 on a Watchguard x750e Core'
date: 2012-02-20T04:35:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=62
permalink: /2012/02/20/how-to-install-pfsense-2-0-on-a-watchguard-x750e-core/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2012/02/how-to-install-pfsense-20-on-watchguard.html
categories:
  - Hardware
  - How To
  - Linux
  - Networking
---
>I picked up a used Watchguard x750e a while back at a flea market (ham fest) for really cheap. I wasn&#8217;t sure what I was going to do with it. I was just excited about finding an fairly good enterprise level firewall at a ham fest.

Fast forward about a year. I came across an article about modding the x750e. After digging deeper I found that people were installing pfsense on them. OMG! I took apart the x750e and sure enough it is essentially just a 1.4 GHz celeron with 512MB ram and and 256MB SanDisk CF card in it. (plus a few other things)

I looked around for a how to but all I could find was a bunch of hard to follow forum posts (and one pretty well documented process, but it seemed to be missing some stuff). Not that they were bad information (I mean I was able to get the thing working based of them), but it was all a bit disconnected.

So here is my walk through. If I&#8217;m missing anything or if I plain got something wrong please leave a comment and I&#8217;ll fix the post.

**OS used:** Windows XP (for hyperterminal. However any terminal capable app will do).
  
**Hyper terminal settings:** Outside of the baud all settings were default.
  
**Pfsense:** current stable release (2.0.1 as of this article)
  
**Replacement CF card:** SanDisk 4 GB Ultra (due to pfsense needing at least a 512MB installation target with current requirements.)
  
**Card reader:** Dynex all in 1. (just a standard usb multi-card reader)
  
**Files needed:** Most of the files needed can be found here: <http://documentation.dbernhardt.com/pfsense/article.html> (This was by far the most complete how to for installing pfsense on a Watchguard that I found.)
  
Serial cable: I used one of the billions that accumulated from working with HP Procurve network equipment.

First take the top cover off the x750e.

<div class="separator" style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-ciXq6KJ-Pso/T0HBgy5LzFI/AAAAAAAAA7A/eINK37Du3T8/s1600/topcoveroff.jpg"><img alt="" src="http://4.bp.blogspot.com/-ciXq6KJ-Pso/T0HBgy5LzFI/AAAAAAAAA7A/eINK37Du3T8/s400/topcoveroff.jpg" width="298" height="400" border="0" /></a>
</div>

There are a bunch of un-soldered connectors for things like VGA, USB, etc&#8230; which can be connected if desired. However for this I wanted to avoid soldering. 🙂

Remove watchguard supplied CF card (256mb in my case) and insert into CF card reader on your computer. (I used a Dynex card reader from the local electronics store). **Disclaimer: you are about to wipe out the factory software that came with your firewall!**

<blockquote class="tr_bq">
  <p>
    physdiskwrite freedosbios.img
  </p>
</blockquote>

Once it&#8217;s done writing the image, copy X750EB6.BIN to bios directory on cf card if it&#8217;s not already there. (Note: There are several x750eB#.bin files that can be used. I&#8217;ve seen reports that 2 and 4 also worked for people. Only 6 worked for me.)

Place the 256MB CF card back in the firewall.

Connect to the console via serial using 9600 baud and power on the firewall.

**Just a FYI to everyone. Yes you are about to flash the bios. This really doesn&#8217;t change anything, it just enables the console output for the bios so we can change some hard drive settings to allow the firewall to boot off a larger media. If you don&#8217;t want to do this, but still want to run something other that the factory software, you can flash Monowall on the 256MB CF card. Monowall requires much less space that pfsense and will run with no bios modifications.**

Once freedos has booted you will see a C:\ prompt:

<blockquote class="tr_bq">
  <p>
    C:\>cd bios<br /> C:\BIOS>awdflash.exe X750EB6.BIN /py /sn /cc /e
  </p>
</blockquote>

<blockquote class="tr_bq">
  <p>
    C:\BIOS>
  </p>
</blockquote>

Power off the firewall and remove the 265mb CF card.

<div class="separator" style="clear: both; text-align: center;">
</div>

Put the 4GB card into the CF reader.

Flash pfsense onto 4 GB card

<blockquote class="tr_bq">
  <p>
    physdiskwrite -u filename.img
  </p>
</blockquote>

-use -u for cf cards larger than 2gb
  
-filename.img should be replaced with the name of the pfsense image you downloaded.

Place 4GB CF card in the firewall now that it has pfsense on it and connect to the console via serial at 19200 baud.

<div class="separator" style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-mD1B9ChO74k/T0HBmLrtpfI/AAAAAAAAA7I/2yyqWcbxzAo/s1600/replacementcf.jpg"><img alt="" src="http://1.bp.blogspot.com/-mD1B9ChO74k/T0HBmLrtpfI/AAAAAAAAA7I/2yyqWcbxzAo/s400/replacementcf.jpg" width="298" height="400" border="0" /></a>
</div>

Power on the firewall.

You should see the post in a few seconds. **Instead of pressing del to enter the bios press tab since you are on a console**.

Go into what is normally the hard drive settings and you should see the CF card detected. If it&#8217;s not or all values are set to 0, set to auto detection and it should set some values. Now change the following:

Set for:

<blockquote class="tr_bq">
  <p>
    IDE Channel 0 Master      [Manual]<br /> Access Mode               [CHS]<br /> Head                      [    2]
  </p>
</blockquote>

Save the BIOS and exit. (If you ever need to remove the CF card again you may have to go back in and reset the above values.
  
Bios will be on 115200 baud from now on after first boot just in case you ever need to get back in there:

Connect to console on 9600 to do initial pfsense configuration. If you see pfsense booting then you should be good to go.

The initial configuration is done from the console. (obviously configuration are going to differ after this point. the rest of this article is just for getting a basic configuration up and running.

Just leave the WAN set to DHCP for now, and give the LAN interface the normal 192.168.1.1 address.
  
For interfaces I like to use SK0 as the wan interface and SK1 and the lan interface. (SK0 is first from the left of the 8. SK1 is the second from the left.

To test you can connect a computer to the LAN interface and see if you get an IP address. If so point your browser at https://192.168.1.1 and you should get a pfsense login page.
  
Login: admin
  
Password: pfsense

The last thing I&#8217;d recomend doing is enabling SSH access. You will need to it fix the fan noise.

pfsense will take you through a small wizard to get the rest of the basic configuration up and running.

**Post x750e install tasks/fixes:**
  
**LOUD NOISES!**
  
The 3 10000 RPM fans in the back of this box present a problem. Noise. There is a wonderful little program that someone wrote called WGXepc. It allows you to clock down the fans in software when pfsense boots. Here is the basic setup:

Quick note about fan speed. (The default speed is FF. We are going to set the speed to 10. That may be too slow though. You should monitor your environment and set accordingly based on need.)

Add the following line to WGXepc.sh
  
/usr/local/etc/rc.d/WGXepc -f 10

Connect using WINscp or ther secure copy tool as root and your pfsense admin password.

copy WGXepc and WGXepc.sh to using WINSCP:
  
/tmp

Login using through SSH (I used putty) with root and your pfsense admin password.

Mount FS as read write
  
/etc/rc.conf\_mount\_rw

cp WGXepc /usr/local/bin
  
cp WGXepc.sh /usr/local/etc/rc.d/

chmod 0755 /usr/local/bin/WGXepc
  
chmod 0755 /usr/local/etc/rc.d/WGXepc.sh

/etc/rc.conf\_mount\_ro

shutdown -r now
  
or
  
run this to set the fan speed now: WGXepc -f 10

All changes done with this command are immediate. Also a system update will remove these settings. You will have to reapply them.

WGXepc will also control the Arm/Disarm light on the front of the x750e. 🙂

Here are the arguments the command accepts:

-f (fan) will return the current fan speed or if followed by a number in hex, 00-FF, will set it.
  
-l (led) will set the arm/disarm to the second argument:
  
red, green, red\_flash, green\_flash, off

**Clock down the CPU**
  
In the web interface goto System->Advanced->Miscellaneous.
  
Enable PowerD

If you see the following in the system log happening a lot:

<blockquote class="tr_bq">
  <p>
    kernel: timecounter TSC must not be in use when changing frequencies; change denied
  </p>
</blockquote>

Add the following to Advanced->System Tunables to fix the issue.

Tunable: kern.timecounter.hardware
  
Value: i8254

**Sources:**
  
<http://nettechonline.net/index.php?option=com_content&view=article&id=78:x700-led-fix-pfsense-on-watchguard&catid=49:pfsense-watchguard&Itemid=58>
  
<http://forum.pfsense.org/index.php?topic=32013.65;wap2>
  
<http://documentation.dbernhardt.com/pfsense/article.html>
  
[http://doc.pfsense.org/index.php/Remount\_embedded\_filesystem\_as\_read-write](http://doc.pfsense.org/index.php/Remount_embedded_filesystem_as_read-write)

There you have it. You should have pfsense working on the x750e hardware. Also if you don&#8217;t want to go the full route of flashing a new bios, you can also put Monowall on the 256MB card that comes with the firewall as mentioned before. Monowall requires a lot less space than pfsense. There are some features you won&#8217;t get with Monowall that exist in pfsense UPNP and OS fingerprinting for example, but Monowall is a really solid firewall platform. pfsense was actually forked from Monowall.

The most aggravating part of all this work was finding a working computer that still has a COM port. 🙂

**Update 2012-02-20:** A couple thing to add. First I didn&#8217;t realize this but it appears that there is no way to re-size the tmp volume. This is significant because it&#8217;s one of two volumes on pfsense embedded that is created as a ram disk (the other is var). This is significant because tmp is where squidguard and other packages store their updates/blacklists. Essentially if you are going to use pfsense in an embedded configuration (on CF card for example) then you are setting up for access control type functions only. Although I was able to get snort working on the x750e with CF card.

Incidentally if you do a df -h from the console, you will see a total of only about 2 GB total for the entire file system. If you are using a 4 GB card the other 2 GB is used for creating a separate slice that can be booted for testing configs and other stuff. Its kinda handy if you have a working config and want to try something out without breaking your &#8220;production&#8221; config.

I guess I&#8217;m going to start researching mounting a SATA or IDE drive in the x750e.

2012-7-14 Update: The other day I was looking at the dashboard and I noticed that I&#8217;ve been running this setup with Snort active for 90 days without reboot. 🙂 So far I&#8217;ve been very happy with the setup. Snort has been a little temperamental and there have been some rules I&#8217;ve had to create exclusions for, but over all this thing has been rock solid.

2013-04-08 Update: Just did an in place upgrade to 2.0.2. I had to reapply the fan speed and LED tweaks, I read that was going to be the case. I also just came across this great article that explains simply how to get LCDproc working correctly. Never thought about installing both the stable and dev packages&#8230; 🙂 <http://www.alteredrealms.com/2012/11/07/watchguard-pfsense-tweaks/>