---
id: 236
title: Get Exchange 2010 transaction log folder sizes with powershell
date: 2012-12-22T19:49:17+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=236
permalink: /2012/12/22/get-exchange-2010-transaction-log-folder-sizes-with-powershell/
categories:
  - Exchange
  - Script
---
Ok so another scrip I just wrote as a result of some recent issues. (thank goodness for powershell). This script will return the size of your transaction log locations on a particular server. It&#8217;s almost universal. The only thing that needs to be customized is the second parameter in the substring method. The section of the script used for getting the folder sizes I got from technet.

<pre class="brush: powershell; auto-links: true; collapse: false; first-line: 1; gutter: true; html-script: false; light: false; ruler: false; smart-tabs: true; tab-size: 4; toolbar: true;">$logpath.SubString(0,10)</pre>

The value of 10 is the number of characters into the string to start trimming. So if you have a string of &#8220;d:\Transactionlogsgohere&#8221;. You would want to set the value to 24. This is also assuming you have created your transaction log locations as sub folders below the substring path. For example:

d:\Transactionlogsgohere\DBlogs01  
d:\Transactionlogsgohere\DBlogs02

<pre class="brush: powershell; auto-links: true; collapse: false; first-line: 1; gutter: true; html-script: false; light: false; ruler: false; smart-tabs: true; tab-size: 4; toolbar: true;">Add-PSSnapin microsoft.exchange.management.powershell.admin -ErrorAction SilentlyContinue

#get current server name
$server = $env:COMPUTERNAME

#determine current server log paths
$DBs = (Get-MailboxDatabase -Server $server | select name,LogFolderPath)
$paths = $DBs | ForEach-Object {
    $logpath = $_.LogFolderPath.ToString()
    $logpath.SubString(0,10)
}
$pathtoscan = $paths | Sort-Object -Unique

#get sizes of each log folder
Write-Host "Scanning --" $server
$folders = Get-ChildItem $pathtoscan | where {$_.PSIsContainer -eq $True} | Sort-Object
foreach ($folder in $folders) {
    $subFolderItems = (Get-ChildItem $folder.FullName | Measure-Object -property length -sum)
    $folder.Name + " -- " + "{0:N2}" -f ($subFolderItems.sum / 1MB) + " MB"
}

</pre>

So like I said, almost universal. 🙂 It&#8217;s very handy for quickly identifying mailbox databases with large amounts of un-truncated transaction logs.