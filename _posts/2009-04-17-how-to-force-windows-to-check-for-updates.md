---
id: 13
title: '>How to force windows to check for updates'
date: 2009-04-17T02:51:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=13
permalink: /2009/04/17/how-to-force-windows-to-check-for-updates/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/how-to-force-windows-to-check-for.html
categories:
  - How To
  - Security
---
>

<div>
  If you want to force Windows XP or Vista to check for updates, there is a simple command you can use from the run dialog.
</div>

<div>
</div>

Click Start->Run

<div>
  Type: wuauclt /detectnow
</div>

<div>
</div>

<div>
  Now just wait a few minutes. If there are available updates it should let you know. This has been pretty handy for testing WSUS functionality.
</div>