---
id: 35
title: '>Exchange send as distribution list'
date: 2009-11-13T22:00:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=35
permalink: /2009/11/13/exchange-send-as-distribution-list/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/11/exchange-send-as-distribution-list.html
categories:
  - How To
  - Script
  - Windows
---
>So I got sick of setting up a mailbox every time someone wanted to send as an email address. I checked into it and it turns out setting send-as rights on a distribution lists is really easy. If you are running Exchange 2007 just open up the exchange shell and type:

get-distributiongroup groupname | add-adpermission -user username -extendedrights Send-As

Props to [this blog](http://www.jasonslater.co.uk/2008/05/16/sending-email-from-a-distribution-list-group/) for putting me in the know.