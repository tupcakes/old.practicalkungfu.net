---
id: 16
title: '>How DNS works'
date: 2009-04-17T15:05:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=16
permalink: /2009/04/17/how-dns-works/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/how-dns-works.html
categories:
  - How To
---
>My wife recently IMed me from work wanting a simple explanation of how DNS works. Here is what I came up with to send to her. There are other nuances to DNS and features that could fill a book but this should explain the basics of name to IP mapping in DNS with ptr records.

domain name address Server address  
www.domain.com -> 192.251.94.101  
domain.com -> 192.251.94.101

Notice that both domain names point to the same server. Because of that both domain names will display the same site.

If you were to have something like:

domain name address Server address  
www.domain.com -> 192.251.94.101  
domain.com -> 192.251.94.101  
ww2.domain.com -> 192.251.94.102

Notice that ww2.domain.com points to a different server.

Sometimes it&#8217;s necessary to have a server for the primary web site and have other servers for other functions on the web site. Those other &#8220;subsites&#8221; would normally require their own domain name address like ww2.domain.com