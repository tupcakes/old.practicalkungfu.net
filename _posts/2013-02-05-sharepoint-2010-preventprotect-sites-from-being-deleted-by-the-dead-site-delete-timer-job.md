---
id: 204
title: 'SharePoint 2010: Prevent/Protect sites from being deleted by the dead site delete timer job'
date: 2013-02-05T14:00:03+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=204
permalink: /2013/02/05/sharepoint-2010-preventprotect-sites-from-being-deleted-by-the-dead-site-delete-timer-job/
categories:
  - Automation
  - Script
  - Sharepoint
---
So here&#8217;s a scenario. Say you turned on the &#8220;Confirmation and Automatic Deletion Settings&#8221; for one of your web applications in central admin. However that setting applies to ALL site collections in that web application, including the root site collection. If you are doing this you probably are using the root level site collection for a landing page or other kind of portal. Oops, that kind of causes a problem. It&#8217;s kind of annoying that there isn&#8217;t a way in central admin to specify exclusions to the automatic site deletion settings.

I&#8217;ll be honest here, I woke up one morning to find out that the web application we have the dead site deletion process turned on for had removed the root level site collection for the web application. I didn&#8217;t know why I didn&#8217;t get my notification email until I restored the site and found that the primary site collection admin wasn&#8217;t me. (oops!)

After doing some searching it appears there is no way to directly exclude sites from this deletion process. However I found mention of a way to force the site use confirmation without the notification email. (see link in resources below.) If you do a:

<pre class="brush: powershell; gutter: false">$site = Get-SPSite http://somesite.domain.com
$site | get-member</pre>

You should see two properties in the list and one method that is relevant to this process, the properties DeadWebNotificationCount, CertificationDate, and the method ConfirmUsage(). DeadWebNotificationCount is the counter for the number of times notification is sent to the site admin. CertificationDate is the last date that the site was confirmed in use. The method ConfirmUsage(), when called, will set the properties DeadWebNotificationCount to zero and CertificationDate to today. This is the same process that happens when you click on the site usage confirmation in the notification email that gets sent out. Alternatively you can also probably directly set the properties, however I haven&#8217;t tried that. I&#8217;d really recommend using the method since it&#8217;s much cleaner.

I threw together the following script. It can be run as a scheduled task as needed. Before running the script, add the site URLs you want to protect to the $protectedsites array. The script then iterates through the array and runs the ConfirmUsage() method on each site.

&nbsp;

<pre class="brush: powershell; gutter: false">#Calls ConfirmUsage() meothod to confirm a site is in use. Loops through an array of sites and calls the ConfirmUsage() method for each site.
#This can be scheduled to run daily to "protect" sites from deletion by the Dead Site Delete timer job.

$protectedsites = @(
"http://rootsitename.domain.com"
"http://anotherimportantsite.domain.com"
)

foreach ($protectedsite in $protectedsites) {
    $site  = Get-SPSite $protectedsite

    #set DeadWebNotificationCount to 0 and CertificationDate to today by calling ConfirmUsage() method.
    $site.RootWeb
    $site.ConfirmUsage()
    $site.Dispose()
}</pre>

&nbsp;

Resources: <http://sharepoint.microsoft.com/Blogs/fromthefield/Lists/Posts/Post.aspx?ID=149>