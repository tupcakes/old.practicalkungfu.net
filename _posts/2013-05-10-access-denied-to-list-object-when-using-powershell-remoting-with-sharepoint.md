---
id: 294
title: Access denied to list object when using powershell remoting with SharePoint
date: 2013-05-10T10:49:33+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=294
permalink: /2013/05/10/access-denied-to-list-object-when-using-powershell-remoting-with-sharepoint/
categories:
  - Sharepoint
---
I came across this one today. Take the following example:

<pre class="lang:ps decode:true brush: powershell; gutter: false">Enter-PSSession -ComputerName servername -Authentication Credssp -Credential domain\username
Add-PsSnapin Microsoft.SharePoint.PowerShell -ErrorAction SilentlyContinue
$SPWeb = Get-SPweb http://portal
$SPWeb.Lists</pre>

When you run the $SPWeb.Lists you will probably get an access denied. it&#8217;s a little strange since the account I was running with was not only a farm admin but also had full control to the web app via the user policy.

Turns out that you need to grant the identity you are using access to the content DBs.

<pre class="lang:ps decode:true brush: powershell; gutter: false ">$w = Get-SPWebApplication -Identity http://portal
$w.GrantAccessToProcessIdentity("domain\username")</pre>

Now rerun the commands and it should work.