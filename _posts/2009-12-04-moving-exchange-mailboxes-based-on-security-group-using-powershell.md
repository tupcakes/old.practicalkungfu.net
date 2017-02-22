---
id: 38
title: '>Moving Exchange mailboxes based on security group using powershell'
date: 2009-12-04T00:45:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=38
permalink: /2009/12/04/moving-exchange-mailboxes-based-on-security-group-using-powershell/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/12/moving-exchange-mailboxes-based-on.html
categories:
  - How To
  - Mac
  - Script
  - Windows
---
>Due to annoyances with our remote access system our users accessed OWA differently depending if they are  
faculty or staff (identified by active directory security group). So I needed a way to migrate only the staff to the new exchange 2007 server. Well all the staff are members of the sonicwall &#8211; staff group. Rather than move each mailbox one at a time to the new server and then redo the group memberships I created a powershell script to do it for me. Here is what it does.

  1. uses Get-QADUser to find all users in an OU
  2. checks each user found to see if it&#8217;s a member of the Sonicwall &#8211; Staff group
  1. if yes
  1. then move the mailbox
  2. remove user from sonicwall &#8211; staff group
  3. add to sonicwall &#8211; exchangehub group

I also added some logic for later when I&#8217;m ready to move the faculty over. Just for clarification, the faculty aren&#8217;t being moved yet because some of them have Macs. Annoyingly Entourage for Mac doesn&#8217;t know how to find a mailbox on an exchange server if the mailbox has been moved.

The script uses the Quest AD powershell addon, so that will have to be install before running.

Here is the script:

> <span style="font-size: xx-small;"># Instruction to add Snap-ins<br />set-Location c:\<br />#add-PSSnapin&nbsp; quest.activeroles.admanagement<br />#Add-PSSnapin -name Microsoft.Exchange.Management.PowerShell.Admin</p> 
> 
> <p>
>   $colResults = Get-QADUser -SearchRoot mtmercy.edu/IT/testing
> </p>
> 
> <p>
>   foreach ($i in $colResults)<br />&nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; #Check if user is in staff group<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; if ((Get-QADUser -identity $i).memberof -eq &#8220;CN=Sonicwall &#8211; Staff,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221;)<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Write-Host &#8220;found staff&#8221;<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Write-Host &#8220;Moving&#8221; $i<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Move-Mailbox -Identity $i.samAccountName -TargetDatabase &#8220;exchangehub\First Storage Group\Mailbox Database&#8221; -SourceMailboxCleanupOptions DeleteSourceMailbox -confirm:$false<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; add-QADGroupMember -identity &#8220;CN=Sonicwall &#8211; Exchangehub,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221; -member $i.dn<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Remove-QADGroupMember -Identity &#8220;CN=Sonicwall &#8211; Staff,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221; -Member $i.dn<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Remove-QADGroupMember -Identity &#8220;CN=Sonicwall &#8211; Faculty,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221; -Member $i.dn<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; <br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; #Check if user is in faculty group<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; if ((Get-QADUser $i).memberof -eq &#8220;CN=Sonicwall &#8211; Faculty,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221;)<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; {<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Write-Host &#8220;found faculty&#8221;<br />#&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; add-QADGroupMember -identity &#8220;CN=Sonicwall &#8211; Exchangehub,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221; -member $i.dn<br />#&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Remove-QADGroupMember -Identity &#8220;CN=Sonicwall &#8211; Staff,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221; -Member $i.dn<br />#&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; Remove-QADGroupMember -Identity &#8220;CN=Sonicwall &#8211; Faculty,OU=Sonicwall,OU=Security Groups,DC=mtmercy,DC=edu&#8221; -Member $i.dn<br />&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; }<br />&nbsp;&nbsp;&nbsp; }</span>
> </p></blockquote> 
> 
> <p>
>   UPDATE:<br />There are some problems with this script. While it does work, it doesn&#8217;t work well. The big problem is memory usage, which I suspect can be fixed. The script also has problems with people who have changed their names, should be easy to fix, or at least work around. Lastly, due to the way I wrote the script it will not move more than one mailbox at a time, which is more of an annoyance than a real problem. I&#8217;m open to any ideas on how to improve this script.
> </p>