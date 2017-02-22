---
id: 157
title: 'Exchange 2010: Re-balance DBs in a DAG'
date: 2012-07-22T02:39:06+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=157
permalink: /2012/07/22/exchange-2010-re-balance-dbs-in-a-dag/
categories:
  - Automation
  - Exchange
  - Script
---
So here is a scenario. You have one or more DAGs in your environment. With at least a few servers and many mailbox DBs per server. Now say you had some kind of nasty event in your environment that caused a failover that has caused all or some of your DBs to become active on your DR/passive nodes. How do you get them back where they are supposed to be.

This may be old news to many of you but SP1 for exchange introduced a few new powershell scripts. One being RedistributeActiveDatabases.ps1. RedistributeActiveDatabases.ps1 will redistribute your mailbox DBs based on activation preference. It&#8217;s very nice for use when patching mailbox server or if there is a failover event. This script along with a few others are located at C:\Program Files\Microsoft\Exchange Server\V14\Scripts. I run it with the following:

<pre class="brush: powershell; gutter: false">RedistributeActiveDatabases.ps1 -DagName DAG01 -BalanceDbsByActivationPreference -ShowFinalDatabaseDistribution -Confirm:$False</pre>

More info on this script at <http://technet.microsoft.com/en-us/library/dd335158.aspx>.

There is one problem with RedistributeActiveDatabases.ps1. It requires organization admin rights to get the DAG information. Thats no good if you don&#8217;t have org admin rights.

<div>
  *The following does require designing your DB names with information you can filter on. For example, have some kind of identifying string in the name so you can tell which server should be the home server. For example DB names could be like, MB01DB01, MB01DB02, etc&#8230; So MB01 would denote the server name and the DB01 part would be the DB number on that server.
</div>

<div>
</div>

<div>
  <pre class="brush: powershell; gutter: false">$destserver = "MB01"
$DBs = Get-MailboxDatabase  | where {$_.Name -like "MB01DB*"}

foreach ($db in $DBs) {
    Move-ActiveMailboxDatabase $db -ActivateOnServer $destserver -Confirm:$false
}</pre>
</div>

<div>
  The script is pretty simple. You specify a destination server and modify the prefix of the DB names. Then run it from the exchange powershell console. You would need to run this for each of your mailbox servers. You could also loop through the mailbox server in your environment then loop through the DBs and move them. I chose not to do that so I could use this as more of a paste into the console type of script.
</div>

<div>
</div>

<div>
  There are probably a few ways to do this. It&#8217;s just a matter of personal preference.
</div>