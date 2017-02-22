---
id: 61
title: '>Open SharePoint 2010 ULS logs on another computer using powershell (Get-SPLogEvent alternative)'
date: 2012-02-13T22:34:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=61
permalink: /2012/02/13/open-sharepoint-2010-uls-logs-on-another-computer-using-powershell-get-splogevent-alternative/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2012/02/open-sharepoint-2010-uls-logs-on.html
categories:
  - Script
  - Sharepoint
---
>Searching through SharePoint 2010 ULS logs can be like looking for a needle in a haystack if you don&#8217;t use the get-splogevent and or merge-splogfile cmdlets. For example if you use those two cmdlets to search for a correlation ID you can get a neatly packaged txt file that tells you just the events related to your correlation ID.

<blockquote class="tr_bq">
  <p>
    $correlationID = &#8220;25ee9d21-d3c6-4075-9420-9b00c874b6ae&#8221;<br />Merge-SPLogFile –Path E:\$correlationID.log –Correlation $correlationID<br />Get-SPLogEvent -File E:\$correlationID.log | Out-GridView
  </p>
</blockquote>

<div>
  However what if you are on a computer that isn&#8217;t part of the SharePoint farm (No SP cmdlets). You can use Import-Csv to load the same file. You just need to tell it to load the headers, (it may be cleaner if you delete the headers from the log file first).
</div>

<div>
  <blockquote class="tr_bq">
    <p>
      $file = Import-Csv -Delimiter &#8220;`t&#8221; .\ULS.log -Header Timestamp,Process,TID,Area,Category,EventID,Level,Message,Correlation<br />$file | Out-GridView
    </p>
  </blockquote>
</div>

<div>
  You can of course add your own filtering to the command, however I like using the Out-Gridview cmdlet since it allows filtering on the various columns.
</div>

<div>
</div>

<div>
  Of course if you have a ULS viewer that would work as well. 🙂
</div>