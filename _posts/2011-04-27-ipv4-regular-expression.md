---
id: 52
title: '>IPv4 Regular Expression'
date: 2011-04-27T16:46:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=52
permalink: /2011/04/27/ipv4-regular-expression/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2010/02/ipv4-regular-expression.html
categories:
  - How To
  - Sharepoint
---
>So a while back someone came to me asking if there was a way to keep people from putting NA in a field on an infopath form where they were supposed to put an IP address. Apparently people liked requesting VM servers without IPs for some reason. 🙂

I found the following regular expression which should require an IPv4 formatted address. It ended up being very handy for the infopath form. Although some users complained about having to have an IP address before requesting the VM.

> (25\[0-5]|2[0-4\]\[0-9\]|\[01]?[0-9\]\[0-9\]?)\.(25\[0-5]|2[0-4\]\[0-9\]|\[01]?[0-9\]\[0-9\]?)\.(25\[0-5]|2[0-4\]\[0-9\]|\[01]?[0-9\]\[0-9\]?)\.(25\[0-5]|2[0-4\]\[0-9\]|\[01]?[0-9\]\[0-9\]?)