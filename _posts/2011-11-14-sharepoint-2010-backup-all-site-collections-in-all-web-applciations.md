---
id: 58
title: '>SharePoint 2010 Backup All Site Collections in All Web applciations'
date: 2011-11-14T17:33:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=58
permalink: /2011/11/14/sharepoint-2010-backup-all-site-collections-in-all-web-applciations/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/11/sharepoint-2010-backup-all-site.html
categories:
  - Automation
  - Script
  - Sharepoint
---
>So with any new SharePoint 2010 farm comes lots of powershell. Since we don&#8217;t have a nice 3rd party backup solution for granular backups setup yet, I decided to write a quick script that will get all site collecitons in all web applications and back them up.

The basic flow of the script is as follows:

  * create directory with current date for backup set
  * loop through all web apps
  * loop through all site collections
  * backup each site collection to a file using the site name as the file name
  * write site name and URL to sitemap.txt file so there is a location record

  * remove backups older than 30 days 

`####<br />#By Mike Tupker<br />#11/14/2011<br />#backups all site collections</p>
<p>Add-PsSnapin Microsoft.SharePoint.PowerShell -ErrorAction SilentlyContinue<br />Start-SPAssignment -Global</p>
<p>$today = Get-Date -format "MM-dd-yyyy-HH.mm.ss"<br />$backuppath = "e:\backups\$today"<br />$logFile = "$backuppath\BackupLog.log"</p>
<p>try<br />{<br />&nbsp;&nbsp;&nbsp; #check if backup directory exist. if not create them.<br />&nbsp;&nbsp;&nbsp; if (-not (Test-Path $backuppath)) {<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; New-Item $backuppath -type directory<br />&nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp; <br />&nbsp;&nbsp;&nbsp; #loop through all web apps<br />&nbsp;&nbsp;&nbsp; foreach ($webapp in Get-SPWebApplication) {<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; #loop through all site in each web apps<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; foreach ($site in Get-SPSite -WebApplication $webapp.url -Limit all | where {$_ -notlike "*Office_Viewing_Service_Cache"}) {<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; #define backup name<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; $name = $site.RootWeb.ToSTring() + ".bak" </p>
<p>&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; #backup site<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Write-Host Site URL: $site.Url<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Write-Host Site Name: $name<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Backup-SPSite -Identity $site.Url -Path $backupPath\$name -UseSqlSnapshot<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; #create site map<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Write-Output "$name,$site.Url" | Out-File -FilePath $backupPath\siteMap.txt -Append<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp; }</p>
<p>&nbsp;&nbsp;&nbsp; #remove old backps older than 30 days<br />&nbsp;&nbsp;&nbsp; $allbackups = Get-ChildItem e:\backups<br />&nbsp;&nbsp;&nbsp; foreach($directory in $allbackups)<br />&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; $creationdate = ((Get-Date) - $directory.CreationTime).Days<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if ($creationdate -gt 30 -and $directory.PsISContainer -eq $True)<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; {$directory.Delete()}<br />&nbsp;&nbsp;&nbsp; }<br />}</p>
<p>catch<br />{<br />&nbsp;&nbsp;&nbsp; write-Host Backup failed. See $logFile for more information.</p>
<p>&nbsp;&nbsp;&nbsp; # Write error message to the log file<br />&nbsp;&nbsp;&nbsp; write "$today&nbsp;&nbsp;&nbsp; Error: $_">>$logFile<br />}</p>
<p>Stop-SPAssignment -Global` 

**Update 2011-11-14**: Changed loop for parsing site collections to exclude the office viewing service cache site.  
**Updated 2012-02-28:**&nbsp;changed code per comment.