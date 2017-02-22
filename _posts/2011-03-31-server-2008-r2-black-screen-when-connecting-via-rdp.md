---
id: 50
title: '>Server 2008 R2 black screen when connecting via RDP'
date: 2011-03-31T15:34:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=50
permalink: /2011/03/31/server-2008-r2-black-screen-when-connecting-via-rdp/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/03/server-2008-r2-black-screen-when.html
categories:
  - How To
  - Windows
---
>So I just tried RDPing to an exchange box that I had been disconnected from. I was greeted with a black screen after logging in. WTF!? I tried killing my session remotely but still had the same issue. Lastly I tried to iLO to the server and logged in. Same black screen. Eek!

After talking to a colleague I found that if I sent a ctl alt del via iLO and then opened the task manager it made the desktop refresh. Still not sure why this has been happening (seen it two times now), but at least it&#8217;s fixable without a reboot&#8230;.so far. 🙂