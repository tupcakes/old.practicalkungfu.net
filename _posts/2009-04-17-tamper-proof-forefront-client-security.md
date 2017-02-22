---
id: 17
title: '>Tamper proof Forefront Client Security'
date: 2009-04-17T18:55:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=17
permalink: /2009/04/17/tamper-proof-forefront-client-security/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/tamper-proof-forefront-client-security.html
categories:
  - Uncategorized
---
>I&#8217;ve been testing Forefront Client Security (FF Client) and so far it is decent. My biggest gripe so far is the lack of tamper protection while it&#8217;s deployed to desktops. However I found a post here: <http://blogs.microsoft.co.il/blogs/yanivf/archive/2009/01/09/temper-protection-in-forefront-client-security.aspx>.

Essentially there are two things that you must do to protect the FF Client all of which can be done through GPO.

  1. Protect the FF client services so only a select few accounts can stop the the services. I&#8217;d recommend setting up a separate security group for this. The important service to protect is the &#8220;Microsoft Forefront Client Security Antimalware Service&#8221;. However it may be a good idea to protect the other one as well called, &#8220;Microsoft Forefront Client Security State Assessment Service&#8221;.
  2. Change permissions on the uninstall information in the registry. (You should use the group that you setup for the rule in step one)  
    HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall

As you can see it&#8217;s not that hard to do and with GPO it should be easy to create a policy that will keep users from shutting off the FF client because they think it&#8217;s making their computer run slow.