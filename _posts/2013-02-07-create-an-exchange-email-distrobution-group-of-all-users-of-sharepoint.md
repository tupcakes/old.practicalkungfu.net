---
id: 253
title: Create an Exchange email distrobution group of all users of SharePoint
date: 2013-02-07T12:43:01+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=253
permalink: /2013/02/07/create-an-exchange-email-distrobution-group-of-all-users-of-sharepoint/
categories:
  - Exchange
  - Script
  - Sharepoint
---
I was asked for a way to email only the people that are using the SharePoint environment. Essentially there was a need to have a distro of just the users of SharePoint for notifying them of maintenance or changes.

The first thing you need to do is create a distribution group in Exchange (this should work for any recent version of exchange). For the purposes of this script I&#8217;ll just call the group &#8220;SharePoint Users&#8221;.

The script itself is actually very simple. Get all sites using Get-SPSite. Then foreach through each site and get the AllUsers Property from the RootWeb. For each user in the AllUsers Property get their AD account based on their email address. Then add the AD user to the distribution group.

<pre class="brush: powershell; gutter: false">Add-PsSnapin Microsoft.SharePoint.PowerShell -ErrorAction SilentlyContinue

$sites = Get-SPSite -Limit All

foreach ($site in $sites) {
	$web = $site.RootWeb
	$users = $web.allusers
	foreach ($user in $users) {
		$ADuser = get-aduser -filter {EmailAddress -eq $user.Email}
		Add-ADGroupMember -Identity "SharePoint Users" -Members $ADuser
	}
}</pre>

The run time will obviously vary based on the size of your organization and SharePoint adoption. Once it&#8217;s done you can check the group members and it should be populated with every person that is using a SharePoint environment.