---
id: 234
title: 'Exchange backups: what DB is my backup application actually backing up now?'
date: 2012-12-20T10:44:28+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=234
permalink: /2012/12/20/exchange-backups-what-db-is-my-backup-application-actually-backing-up-now/
categories:
  - Exchange
---
We use Tivoli TDP for Exchange. Right now I&#8217;m fighting my way through some backup issues. TDP doens&#8217;t really give an indication of which DB is being backed up currently when using an automated backup script. I needed to know! I ran Get-MailboxDatabase | get-member to see the possible properties. Turns out there is a property that will say if a backup is in progress, backupinprogress.

So I ran:

<pre class="brush: powershell; gutter: false">Get-MailboxDatabase -Server servername | Sort-Object name | ft name,backupinprogress</pre>

It did a nice job of showing me the server name but no bakckup status. (huh?) Off to technet! <http://technet.microsoft.com/en-us/library/bb124924(v=exchg.141).aspx>

Turns out there is a -status switch that can be used with Get-MailboxDatabase that will return some extra information. I tried it and sure enough.

<pre class="brush: bash; gutter: false">Get-MailboxDatabase -Server servername -Status | Sort-Object name | ft name,backupinprogress
Name             BackupInProgress
----             ----------------
DB01                     False
DB02                     False
DB03                     False
DB04                     False
DB05                     False</pre>

Now I know which DBs are still being backed up if a backup job is running. I&#8217;m sure other backup tools may provide more insight into their backup processes. Just thought this would be handy.