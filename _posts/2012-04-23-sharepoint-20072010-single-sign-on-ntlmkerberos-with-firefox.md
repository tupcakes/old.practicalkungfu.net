---
id: 81
title: SharePoint 2007/2010 single sign on (NTLM/Kerberos) with Firefox
date: 2012-04-23T15:57:44+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=81
permalink: /2012/04/23/sharepoint-20072010-single-sign-on-ntlmkerberos-with-firefox/
categories:
  - Sharepoint
---
This is probably old news but I thought I&#8217;d share anyway. To get a single sign on experience (no authentication prompt for domain joined machines) do the following.

Open Firefox and goto:
  
about:config

Search for:
  
network.automatic-ntlm-auth.trusted-uris

Add:
  
domain.com
  
or
  
subdomain.domain.com

Note: domain.com has an implied wildcard so any subdomain will be included. Multiple domains can also be added if they are seperated by commas.