---
id: 21
title: '>Network monitoring'
date: 2009-04-28T14:49:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=21
permalink: /2009/04/28/network-monitoring/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/network-monitoring.html
categories:
  - Monitoring
  - Networking
  - Security
---
>I finally decided on a network monitoring solution for the campus. I decided to go with Zenoss since it handled auto discovery pretty well and the stack installer was really easy. I also tried zabbix and cacti. Zabbix has an installer script that is also wonderfull. I think Zabbix came in second for me.

I also got a Snort IDS setup on a monitor (span) port as well as NTOP. It&#8217;s been running for less than 24 hours and it&#8217;s already given me a good look at how much junk there is going accross the open wireless network and student networks. Thank god for 802.1x and vlan assignments. 🙂

I&#8217;ll probably scale it back to just the employee network and lab computers since we are not allowed to lock out student computers.