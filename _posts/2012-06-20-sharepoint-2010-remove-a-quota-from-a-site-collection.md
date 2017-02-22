---
id: 103
title: 'SharePoint 2010: Remove a quota from a site collection'
date: 2012-06-20T20:42:58+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=103
permalink: /2012/06/20/sharepoint-2010-remove-a-quota-from-a-site-collection/
categories:
  - Script
  - Sharepoint
---
Back to some basic stuff today. I was trying to create a process to pre-stage empty site collections for content to be migrated. In part of the pre-staging process I wanted to wipe out the default quota, using powershell, on the site for the duration of the migration.

Turns out it is easy.

<pre class="brush: powershell; gutter: true">$site = Get-SPSite "someURL"
$site.Quota = $null</pre>

That will force the site collection back to an individual quota and set it to unlimited. If you wanted to set the max and warning sizes you can use the Set-SPSite cmdlet. (The below example is from Technet: http://technet.microsoft.com/en-us/library/ff607958.aspx

<pre class="brush: powershell; gutter: true">Set-SPSite –identity "http://sitename" -MaxSize 4000000 -WarningSize 2000000</pre>

Interestingly you can&#8217;t just set MaxSize and WarningSize to 0. Apparently if you try that it will fail since the quota template is still technically applied to the site.