---
id: 56
title: '>Vista Group Policy Preferences and Scheduled Tasks not pushing'
date: 2011-06-21T02:31:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=56
permalink: /2011/06/21/vista-group-policy-preferences-and-scheduled-tasks-not-pushing/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/06/vista-group-policy-preferences-and.html
categories:
  - Group Policy
  - How To
  - Windows
---
>So I had need to deploy a wireless profile (and key) to a bunch of Vista machines last week. Since group policy doesn&#8217;t support pushing personal wireless encryption keys I had to do a scheduled task preference that runs a script that calls an import command. The policy worked perfectly on Windows 7 however when I tested on a Vista machine I found that no scheduled task had been created yet.

<div>
</div>

<div>
  Fast forward through many hours of banging my head against the GPMC&#8230;.
</div>

<div>
</div>

<div>
  I called MS support and they confirmed that this is a known bug. Apparently Windows vista machines cannot have vista or later scheduled task preferences pushed to them. I was however able to get around the issue by creating a simple, XP era, task using preferences that runs at logon. For some reason the Vista client can receive that correctly.
</div>

<div>
</div>

<div>
  So apparently MS is aware of the issue and they are working on a fix&#8230;(deployment date TBD&#8230;.) 😉
</div>