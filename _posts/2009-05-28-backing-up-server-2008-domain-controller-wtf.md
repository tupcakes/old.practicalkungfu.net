---
id: 25
title: '>Backing up Server 2008 Domain Controller (WTF!!!)'
date: 2009-05-28T20:28:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=25
permalink: /2009/05/28/backing-up-server-2008-domain-controller-wtf/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/05/backing-up-server-2008-domain.html
categories:
  - How To
  - Windows
---
>WTF is all I can say about the new Windows server backup that replaced ntbackup. Yes SB has some nice features and I can almost understand the lack of support for tape drives, but lets be honest. How good of a practice is it to keep your backups on the very server you are backing up!!!

You can however use some command line arguments to do a full backup of a 2008 domain controller to a network share.

wbadmin start backup -backuptarget:\\server\backups\dc -include:c: -AllCritical -quiet

From what I&#8217;ve read there is no way to do incremental backups like this so it will overwrite the backup with another full backup everytime.

Oh and you can&#8217;t do system state backups to network shares either. There is however a [registry hack](http://support.microsoft.com/kb/944530) (KB 944530) that will allow you to use Server Backup to backup the system state to the c drive. You can then move the system state backup to a network share with a script.

I&#8217;m thinking about setting up an openfiler iscsi server and trying to backup through iscsi.

If anyone knows of a better way to backup a 2008 controller please post a comment.

Update: This is a REALLY late update, but MS has changed this. SB in 2008 R2 also supports backup to network share.