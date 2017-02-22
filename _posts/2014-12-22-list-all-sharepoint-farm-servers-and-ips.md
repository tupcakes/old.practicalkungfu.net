---
id: 348
title: List all SharePoint Farm servers and IPs
date: 2014-12-22T08:39:08+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=348
permalink: /2014/12/22/list-all-sharepoint-farm-servers-and-ips/
categories:
  - Script
  - Sharepoint
---
Not much to see here. I just had need of simply listing all the server names and IPs for all servers in a farm.

<pre class="lang:ps decode:true ">$report = @()

$servers = Get-SPServer | where {$_.role -ne "Invalid"}

$servers | foreach-object {
    $test = Test-Connection -Count 1 $_.Address

    $obj = New-Object System.Object
    $obj | Add-Member -type NoteProperty -Name ServerName -Value $_.Address
    $obj | Add-Member -type NoteProperty -Name IPV4Address -Value $test.IPV4Address
    $report += $obj
}

$report</pre>

The script gets all SharePoint application servers in the farm. Then iterates through them and tests if they are live. The test-connection is simply to get the IP address of the server. The name of the server and the IP address are thrown into a custom object. Then the report is called.

&nbsp;