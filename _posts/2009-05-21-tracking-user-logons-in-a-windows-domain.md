---
id: 23
title: '>Tracking user logons in a Windows domain'
date: 2009-05-21T16:08:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=23
permalink: /2009/05/21/tracking-user-logons-in-a-windows-domain/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/05/tracking-user-logons-in-windows-domain.html
categories:
  - How To
  - Script
  - Security
---
>Here is are a couple small scripts that can be deployed through GPO that will log the time, computer name, and user name of domain computer logon events.

Essentially there are two scripts, one that is run at logon and the other is run at log off. The logon script is as follows:

> @ECHO OFF  
> echo %date%,%time%,logon,%username% >> &#8220;\\server\LoginTracking\logs\computers\computer-%computername%.txt&#8221;  
> echo %date%,%time%,logon,%computername% >> &#8220;\\server\LoginTracking\logs\users\user-%username%.txt&#8221;

The script will write the date, time, event type (logon/logoff), and computer or username to a log. The script actually write two seperate logs, one by computer name and the other by username. That way you can see what computers a user has been logging into and what users have been logging into a specific computer.

The logoff script is identical except for the event type.

> @ECHO OFF  
> echo %date%,%time%,logoff,%username% >> &#8220;\\server\LoginTracking\logs\computers\computer-%computername%.txt&#8221;  
> echo %date%,%time%,logoff,%computername% >> &#8220;\\server\LoginTracking\logs\users\user-%username%.txt&#8221;