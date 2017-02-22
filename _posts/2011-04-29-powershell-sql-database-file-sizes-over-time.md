---
id: 54
title: '>Powershell: SQL Database File Sizes Over Time'
date: 2011-04-29T15:35:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=54
permalink: /2011/04/29/powershell-sql-database-file-sizes-over-time/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/04/powershell-sql-database-file-sizes-over.html
categories:
  - How To
  - MS SQL
  - Script
---
>Recently I had need to track the file sizes of databases (MDF files attached to a SQL 2005 server) over time. I setup a scheduled task to run every hour which runs a powershell script. The script gets the file sizes of all MDFs in a directory on a remote computer.

#Construct the filename Date-time  
$date = get-date  
$filename = &#8220;MDF-{0}{1:d2}{2:d2}-{3:d2}{4:d2}.txt&#8221; -f $date.year,$date.month,$date.day,$date.hour,$date.minute

$spacestats = @()  
$spacestats = Get-ChildItem &#8220;\\servernamev$\Microsoft SQL Server\MSSQL.12\MSSQL\Data&#8221; *.mdf | Select-Object Name, @{Name=&#8221;Mbytes&#8221;;Expression={[math]::Round($_.Length / 1Mb)}}  
$spacestats | ConvertTo-Csv -NoTypeInformation | Out-File $filename

The idea is that later I&#8217;ll be able to compile this historical data and track DB growth over time. It probably isn&#8217;t the most elegant solution but it gets the job done.