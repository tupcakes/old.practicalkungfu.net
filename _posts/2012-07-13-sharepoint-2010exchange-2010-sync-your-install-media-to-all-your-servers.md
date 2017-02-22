---
id: 142
title: 'SharePoint 2010/Exchange 2010: Sync your install media to all your servers'
date: 2012-07-13T21:35:53+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=142
permalink: /2012/07/13/sharepoint-2010exchange-2010-sync-your-install-media-to-all-your-servers/
categories:
  - Exchange
  - Script
  - Sharepoint
---
So I actually adapted this from an Exchange 2010 script I just wrote to do the same thing. Say it&#8217;s time to install a CU/RU, service pack, or some other addon that requires binaries be installed on all SharePoint servers. There are a few ways you can do this.

  * Manually copy all the files through windows explorer.
  * use robocopy commands that copies the folder. each server would have it&#8217;s own robocopy command.
  * SharePoint 2010 and Exchange 2010 allow us to get a list of the servers in the environments. We can use powershell with robocopy to programatically push the install folder to the servers.

<div>
  The two scripts are virtually identical with the exception of how the list of servers is acquired. SharePoint uses get-spserver while exchange uses get-exchangeserver.
</div>

<div>
</div>

<div>
  I also added an if statement to make sure it doesn&#8217;t bother trying to copy the files to itself.
</div>

**SharePoint 2010**

The script doesn&#8217;t actually directly reference any servers in the environment so it&#8217;s completely portable.

<pre class="brush: powershell; gutter: true">$servers = Get-SPServer | where {$_.role -ne "Invalid"}

foreach ($server in $servers) {
    if ($server.address -ne $env:COMPUTERNAME) {
		$name = $server.name
        robocopy \\$env:COMPUTERNAME\e$\_Installs \\$name\e$\_Installs /MIR /FFT /Z /XA:H /W:5 /r:2
    }
}</pre>

&nbsp;

**Exchange 2010**

Again no environment specific information is used so it&#8217;s portable.

<pre class="brush: powershell; gutter: true">$servers = Get-ExchangeServer

foreach ($server in $servers) {
    if ($server.name -ne $env:COMPUTERNAME) {
        robocopy \\$env:COMPUTERNAME\C$\_Install \\$server\c$\_Install /MIR /FFT /Z /XA:H /W:5 /r:2
    }
}</pre>

&nbsp;