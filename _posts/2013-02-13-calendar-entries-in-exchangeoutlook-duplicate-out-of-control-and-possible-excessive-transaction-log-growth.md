---
id: 260
title: Calendar entries in Exchange/Outlook duplicate out of control and possible excessive transaction log growth
date: 2013-02-13T11:46:43+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=260
permalink: /2013/02/13/calendar-entries-in-exchangeoutlook-duplicate-out-of-control-and-possible-excessive-transaction-log-growth/
categories:
  - Exchange
---
I feel a little late to the party on this but I ran across this issue today. <http://support.microsoft.com/kb/2814847>. There is an issue in iOS 6.1 that can cause calendar entries to start duplicating out of control. A side effect is that it will also cause transaction logs to grow out of control as well.

According to the article there isn&#8217;t much that can be done to prevent it. The official recommendations as of this posting are to:

  * Remove and recreate the device partnership
  * Create a custom throttling policy
  * Block all iOS 6.1 users

Removing the recreating the partnership does seem to work in the one case I&#8217;ve dealt with so far. Still this is a very nasty issue due to the transaction log growth. the throttling policy will help mitigate the issue but not stop it. It will just slow the mailbox totaldeleteditemsize and log growth.

One option for proactively detecting users where this is happening is to compare the totaldeleteditemsize from an hour or two ago to what it&#8217;s currently at now.

<pre class="lang:ps decode:true brush: powershell; gutter: false ">Get-MailboxStatistics -Database DBname | select displayname,TotalItemSize,TotalDeletedItemSize,ItemCount | sort ItemCount -Descending</pre>

Thats good for a visual inspection but if you need something that can be alerted on the following script also works. It compares a previous value of totaldeleteditemsize to the current value for all users on a server (I tried all org users and the result size broke poweshell) and then reports anyone who has had that value grow over 1 GB since the last run. a time of 1-2 hours between runs should be good enough to get results. The results can then be reviewed to see if there is cause for concern and investigated further.

<pre class="lang:ps decode:true brush: powershell; gutter: false ">$server = "servername"

$yesterfilename = $server + "_yesterusersizes.txt"
$filename = $server + "_usersizes.txt"

Remove-Item $yesterfilename -Force -Confirm:$False
Rename-Item $filename -NewName $yesterfilename -Force -Confirm:$False

Get-Mailbox -Server $server -ResultSize Unlimited | Get-MailboxStatistics | select displayname,@{label=”TotalItemSizeMB”;expression={$_.TotalItemSize.Value.ToMB()}},@{label=”TotalDeletedItemSizeMB”;expression={$_.TotalDeletedItemSize.Value.ToMB()}},ItemCount,LegacyDN | sort TotalDeletedItemSizeMB -Descending | Export-Csv -NoTypeInformation $filename

$yesteruserssizes = Import-Csv $yesterfilename

Foreach ($user in $yesteruserssizes) {
&nbsp;&nbsp;&nbsp; $Current = Get-MailboxStatistics $user.LegacyDN | select displayname,@{label=”TotalItemSizeMB”;expression={$_.TotalItemSize.Value.ToMB()}},@{label=”TotalDeletedItemSizeMB”;expression={$_.TotalDeletedItemSize.Value.ToMB()}},ItemCount,LegacyDN

&nbsp;&nbsp;&nbsp; $diff = $Current.TotalDeletedItemSizeMB - $user.TotalDeletedItemSizeMB
&nbsp;&nbsp;&nbsp; If ($diff -gt 1024) {
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; $Current | ft
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Write-Host "Delta for" $current.displayname":" $Diff | out-file -Append Report.txt -Force
&nbsp;&nbsp;&nbsp; }
}</pre>

No word on if this is fixed in iOS 6.1.1 that I&#8217;ve heard about.

I also saw this blog post which does a very good job of describing the issue and expands a bit more on MS&#8217; workarounds. <http://eightwone.com/2013/02/08/yaii-or-yet-another-iphone-issue/>

Update 2013-02-14: Apple has release an article about the issue. http://support.apple.com/kb/TS4532. It explains that the issue occurs when you respond to a modification to a single event in a meeting series. Apparently a fix is coming in an update. No word when that will be though.

Update 2013-02-19: Apple released 6.1.2. Apparently this does fix the issue. YAY!