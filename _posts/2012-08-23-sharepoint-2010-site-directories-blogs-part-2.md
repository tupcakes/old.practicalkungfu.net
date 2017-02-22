---
id: 184
title: 'SharePoint 2010: Site Directories (Blogs) &#8211; Part 2'
date: 2012-08-23T10:11:57+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=184
permalink: /2012/08/23/sharepoint-2010-site-directories-blogs-part-2/
categories:
  - Automation
  - Script
  - Sharepoint
---
In the last post I used a script to create a site directory of all sites. Now what if you don&#8217;t want all sites. What if you want to create a directory of all blog sub sites.

<pre class="brush: powershell; gutter: false">Add-PsSnapin Microsoft.SharePoint.PowerShell -ErrorAction SilentlyContinue

$SPAssignment = Start-SPAssignment

#moddify these variables for the directory location
$URL = "https://webapp.domain.com"
$title = "Site List"
$description = "This is a list of all sites that have been created."

#delete the previous list
$SPWeb = Get-SPWeb $URL
$list = $SPWeb.Lists[$title]
$list.AllowDeletion = $true
$list.Update()
$list.Delete()

#create list
$SPTemplate = $SPWeb.ListTemplates["Custom List"]
$SPWeb.Lists.Add($title,$description,$SPTemplate)

#add fields to list
$spFieldType = [Microsoft.SharePoint.SPFieldType]::Text
$list = (Get-SPWeb -identity $URL).Lists[$Title]
$List.Fields.Add("Name",$spFieldType,$false)
$List.Fields.Add("URL",$spFieldType,$false)
$List.Fields.Add("Owner",$spFieldType,$false)
$List.Fields.Add("Secondary Contact",$spFieldType,$false)

#create default view with added columns
#http://blogs.technet.com/b/heyscriptingguy/archive/2010/09/22/use-powershell-to-manage-lists-views-and-items-in-sharepoint.aspx
$listURL = $URL + "/Lists/Site%20List/AllItems.aspx"
$SPWeb = Get-SPWeb $URL
$spView = $SPWeb.GetViewFromUrl($listURL)
$spView.ViewFields.Delete("LinkTitle")
$spView.ViewFields.Add("Name")
$spView.ViewFields.Add("URL")
$spView.ViewFields.Add("Owner")
$spView.ViewFields.Add("Secondary Contact")
$spView.Update()

foreach ($web in get-spsite -limit all | where {$_ -notlike "*Office_Viewing_Service_Cache"}  | Get-SPWeb -limit all  | Where {$_.WebTemplate -eq "BLOG"}) {
    $site = Get-SPSite $web.site
	$newItem = $list.Items.Add()
	$newItem["Title"] = $web.URL
    $newItem["Name"] = $web.Name
	$newItem["URL"] = $web.Url
    $newItem["Owner"] = $site.Owner.displayname
    $newItem["Secondary Contact"] = $site.SecondaryContact.DisplayName
	$newItem.Update()
}

Stop-SPAssignment $SPAssignment</pre>

This script is essentially the same. I did make a few changes in the fields and the view. The most notable change is in the get-spsite cmdlet in the foreach loop.

<pre class="brush: powershell; gutter: false">get-spsite -limit all | where {$_ -notlike "*Office_Viewing_Service_Cache"}  | Get-SPWeb -limit all  | Where {$_.WebTemplate -eq "BLOG"}</pre>

The first where that exludes sites like &#8220;*Office\_Viewing\_Service_Cache&#8221; just gets rid of that site collection which is something we don&#8217;t want list in a site directory. Then it returns all sub webs from the collection and then filters by webs created with the blog site template.

Again this script will destroy the existing list during each run. That works fine if all you want is a site directory. But what if you want to track other information thats not retrievable using the SharePoint object model? For example if you need to manually update data in an additional column thats not set by the script. I&#8217;ll go into that in the next post.