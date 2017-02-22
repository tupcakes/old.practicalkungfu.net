---
id: 6
title: '>Install Nessus on Ubuntu'
date: 2009-04-13T23:36:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=6
permalink: /2009/04/13/install-nessus-on-ubuntu/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/install-nessus-on-ubuntu.html
categories:
  - Security
---
>Yeah I know it&#8217;s not that hard to do but here are the basics.

> sudo aptitude install nessus nessusd nessus-plugins  
> sudo nessus-adduser  
> sudo /etc/init.d/nessud restart

Now you should be able to connect to the nessus daemon from the client. There is a free windows client available for download from the nessus site if you prefer windows.