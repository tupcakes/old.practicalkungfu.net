---
id: 31
title: '>Vista is slow &#8211; Stupid Windows trying to be helpful'
date: 2009-07-22T13:36:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=31
permalink: /2009/07/22/vista-is-slow-stupid-windows-trying-to-be-helpful/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/07/up-until-recently-ive-been-fighting.html
categories:
  - How To
  - Networking
  - Windows
---
>Up until recently I&#8217;ve been fighting Vista and it&#8217;s performance on any kind of hardware. A colleague of mine pointed me toward this annoyingly easy fix. Vista has something called network auto-tuning. Essentially it&#8217;s Vista trying to be helpful with your network connection and other network devices not know how to handle what vista is doing. (Thats my interpretation of the cause, if that doesn&#8217;t sound right to you please feel free to correct me).

To disable auto-tuning open an administrator command prompt and type:

> NETSH INTERFACE TCP SET global autotuninglevel=disabled

Then reboot. You can also try setting it to restricted first as well to see if that will help. The restricted setting will not completely disable it but may help with some network devices.

I can&#8217;t guarantee that this will completely fix the issue, but for me it&#8217;s done wonders. Now my Vista machine is almost as fast as my XP box.

There are some other setting for the auto-tuning level. [This article](http://blogs.msdn.com/wndp/archive/2007/07/05/receive-window-auto-tuning-on-vista.aspx) on the Windows Core Networking blog has a very good explanation of some of them. The [cable guy article here](http://technet.microsoft.com/en-us/magazine/2007.01.cableguy.aspx) also has a petty good article on the details of what it&#8217;s doing.

UPDATE (2009-07-25): I just found out that if you have Vista SP2 installed you must first disable the network heuristics in vista before you can turn disable autotuning.

> netsh interface tcp set heuristics disabled

Just run the above command before running the command to disable heuristics.