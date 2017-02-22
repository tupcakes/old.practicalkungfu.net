---
id: 20
title: '>Windows Server SNMP Monitoring'
date: 2009-04-26T23:13:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=20
permalink: /2009/04/26/windows-server-snmp-monitoring/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/windows-server-snmp-monitoring.html
categories:
  - How To
  - Monitoring
  - Windows
---
>Now that I have some basic monitoring setup for our procurve infrastructure, I&#8217;m going to be working on getting some monitoring setup for the servers. I came across [SNMP-Informant](http://www.snmp-informant.com/) which looks interesting. Although I&#8217;d like to try and do the monitoring without installing a bunch of 3rd party software on our servers. I&#8217;ll be shooting for just using hte SNMP service that can be installed with windows server.

I also found [this](http://www.appdeploy.com/tips/detail.asp?id=59), which indicates how to install services from the command line Sysocmgr.exe. It may work for installing the SNMP service via GPO startup script. [Here](http://support.microsoft.com/default.aspx?scid=kb%3ben-us%3b222444) is the MS support article for those interested. According to the MS support article sysocmgr.exe does not apply to Server 2008. There is a good article [here](http://myitforum.com/articles/15/view.asp?id=8792) that gives an example for doing this.

For feature management in server 2008 use [ocsetup.exe](http://support.microsoft.com/kb/936209). The article [here](http://technet.microsoft.com/en-us/magazine/dd673656.aspx) shows how to install the SNMP feature as well as some other features and roles.