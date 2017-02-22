---
id: 277
title: Windows Update error code 8024402F on Hyper-V guests
date: 2013-03-11T20:19:37+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=277
permalink: /2013/03/11/windows-update-error-code-8024402f-on-hyper-v-guests/
categories:
  - Sharepoint
  - Virtualization
---
OK so this was a weird one. I updated my lab environment which runs on a Windows 8 Hyper-V install. I tried installing the pre-requirements for SharePoint 2013 but it kept failing. I then tried running Windows Update and got a code 8024402F. Everything that people were saying indicated that it was indicating a connection issue. Um, what? I can browse the web on this VM, why isn&#8217;t it windows update working? After some digging online I found a response to a post about a similar question.

[http://www.experts-exchange.com/Microsoft/Applications/Virtual\_Server/Hyper-V/Q\_26941221.html](http://www.experts-exchange.com/Microsoft/Applications/Virtual_Server/Hyper-V/Q_26941221.html)

[The poster blamed the offloading settings on the broadcom NIC they were using. Mine is a realtek, however when I checked it did have offloading settings. I decided what the heck and disabled the following NIC adapter settings.](http://www.experts-exchange.com/Microsoft/Applications/Virtual_Server/Hyper-V/Q_26941221.html)

  * <span style="line-height: 14px;">ARP offloading</span>
  * Large send offloading
  * TCP checksum offloading
  * UDP checksum offloading

Example below:

[<img class="alignnone size-full wp-image-278" alt="Windows Update error code 8024402F on Hyper-V guests" src="http://practicalkungfu.net/wp-content/uploads/2013/03/Windows-Update-error-code-8024402F-on-Hyper-V-guests.png" width="908" height="582" srcset="http://practicalkungfu.net/wp-content/uploads/2013/03/Windows-Update-error-code-8024402F-on-Hyper-V-guests.png 908w, http://practicalkungfu.net/wp-content/uploads/2013/03/Windows-Update-error-code-8024402F-on-Hyper-V-guests-300x192.png 300w, http://practicalkungfu.net/wp-content/uploads/2013/03/Windows-Update-error-code-8024402F-on-Hyper-V-guests-624x399.png 624w" sizes="(max-width: 908px) 100vw, 908px" />](http://practicalkungfu.net/wp-content/uploads/2013/03/Windows-Update-error-code-8024402F-on-Hyper-V-guests.png)

&nbsp;

After disabling the offloading features shown above, the SharePoint 2013 pre-requisite installer started actually working. One of those quirks of running a Hyper-V lab I guess.

Update: well I guess I was wrong on this. the issue started happening again. I&#8217;ll update the post when I find the resolution.

Update: Its been a while since I updated this but I was not able to find an easy solution with the existing setup. I ended up buying a dedicated NIC for the hyper-v guests. So the system has two, one for the guests and another dedicated for the OS.