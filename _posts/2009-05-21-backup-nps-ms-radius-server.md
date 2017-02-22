---
id: 24
title: '>Backup NPS (MS RADIUS) server'
date: 2009-05-21T16:49:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=24
permalink: /2009/05/21/backup-nps-ms-radius-server/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/05/backup-nps-ms-radius-server.html
categories:
  - How To
  - Script
---
>I found this netsh command for Network Policy Server in [Technet](http://technet.microsoft.com/en-us/library/cc732059%28WS.10%29.aspx) recently and thought I&#8217;d share.

netsh nps export &#8220;C:\config.xml&#8221; exportPSK = YES

This will export the entire NPS config to an XML file. That file can be imported into another NPS server by doing:

netsh nps import filename=&#8221;path\file.xml&#8221;

I am thinking about using this to keep redundant NPS boxes in &#8220;sync&#8221; for fail over. Regardless if my &#8220;sync&#8221; idea pans out its a good easy way to backup NPS.