---
id: 29
title: '>So your replica is inconsistent'
date: 2009-07-17T01:32:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=29
permalink: /2009/07/17/so-your-replica-is-inconsistent/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/07/so-your-replica-is-inconsistent.html
categories:
  - How To
  - Windows
---
>We just started testing Microsoft Data Protection Manager 2007 using an openfiler iscsi san and a 2008 box. So far it&#8217;s &#8220;working&#8221;. A lot of the time though I will get a message saying that the replica is inconsistent. Even after doing a consistency check and sync. After trying many thing I stumbled across this [article](http://www.jadota.com/2008/12/data-protection-manager-2007-replica-is-inconsistent/). It says that manually specifying the DPM server does something to fix this issue.

Here is the command:  
&#8220;C:\Program Files\Microsoft Data Protection Manager\DPM\bin\setdpmserver&#8221; -dpmservername <span style="font-style: italic;">servername</span>

I ran this on our exchange 2003 server, which was having this issue. So far so good. It&#8217;s still preforming it&#8217;s check but at least it&#8217;s not immediately showing the replica and inconsistent. I&#8217;m keeping my fingers crossed.