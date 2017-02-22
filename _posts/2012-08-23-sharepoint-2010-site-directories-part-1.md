---
id: 179
title: 'SharePoint 2010: Site Directories &#8211; Part 1'
date: 2012-08-23T09:35:32+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=179
permalink: /2012/08/23/sharepoint-2010-site-directories-part-1/
categories:
  - Automation
  - Script
  - Sharepoint
---
There has been some talk about SharePoint site directories at work recently. As you may or may not know the old SharePoint 2007 site directory template was dropped. This isn&#8217;t that big of a deal since that feature was pretty limited in it&#8217;s usefulness (in my opinion). However with a little powershell magic it&#8217;s possible to create a site directory and have it updated automatically.

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
$List.Fields.Add("URL",$spFieldType,$false)
$List.Fields.Add("Owner",$spFieldType,$false)
$List.Fields.Add("Secondary Contact",$spFieldType,$false)

#create default view with added columns
#http://blogs.technet.com/b/heyscriptingguy/archive/2010/09/22/use-powershell-to-manage-lists-views-and-items-in-sharepoint.aspx
$SPWeb = Get-SPWeb $URL
$spView = $SPWeb.GetViewFromUrl("/Lists/Site%20List/AllItems.aspx")
$spView.ViewFields.Add("URL")
$spView.ViewFields.Add("Owner")
$spView.ViewFields.Add("Secondary Contact")
$spView.Update()

foreach ($site in get-spsite https://webapp.domain.com/sites/* -limit all | where {$_ -notlike "*Office_Viewing_Service_Cache"}) {
	$newItem = $list.Items.Add()
	$newItem["Title"] = $site.RootWeb
	$newItem["URL"] = $site.Url
    $newItem["Owner"] = $site.Owner.displayname
    $newItem["Secondary Contact"] = $site.SecondaryContact.DisplayName
	$newItem.Update()
}

Stop-SPAssignment $SPAssignment</pre>

This script is actually pretty simple. The flow is as follows.

  1. set variables.
  2. delete the old list if it exists.
  3. create a new custom list.
  4. add fields to the list.
  5. create a default view for the list
  6. add each site collection to the list (rootweb name, URL, primary site collection admin, and secondary site collection admin)

Since the script destroys the previous list during each it&#8217;s not very flexible, but is certainly simple. In the next post I&#8217;ll show how to create a site directory of just one site type.

&nbsp;