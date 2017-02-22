---
id: 46
title: '>Why is deploying a site template solution not obvious? (AKA the Sharepoint Fab 40)'
date: 2010-08-19T20:55:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=46
permalink: /2010/08/19/why-is-deploying-a-site-template-solution-not-obvious-aka-the-sharepoint-fab-40/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2010/08/why-is-deploying-site-template-solution.html
categories:
  - How To
  - Sharepoint
  - Windows
---
>So I&#8217;ve been trying to deploy some of the MS site template solutions that are freely available. Until recently I didn&#8217;t realize that &#8220;ApplicationTemplateCore.wsp&#8221; isn&#8217;t a fancy placeholder name for the solution you are trying to deploy, it&#8217;s a solution. So if you ever get any of those freebee site templates from MS like the helpdesk or knowledge base, here is how to get them to deploy. (note the syntax for powershell and moddify accordingly if you are using the old school cmd.exe).

Add and deploy the Applicaiton template core solution  
.\stsadm.exe -o addsolution -filename &#8220;C:\Users\tupkem01a\Desktop\ApplicationTemplateCore.wsp&#8221;  
.\stsadm.exe -o deploysolution -name ApplicationTemplateCore.wsp -immediate -allowgacdeployment

Add and deploy site template soltions  
.\stsadm.exe -o addsolution -filename &#8220;C:\Users\tupkem01a\Desktop\KnowledgeBase.wsp&#8221;  
.\stsadm.exe -o deploysolution -name KnowledgeBase.wsp -immediate -allowgacdeployment

Not too hard but not at all obvious in my opinion. Don&#8217;t forget to run elevated if you are on server 2008.

Update: I forgot to mention this is for Sharepoint 2007. Also I would not recommend installing them because it will make your migration to Sharepoint 2010 harder.