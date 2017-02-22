---
id: 186
title: 'SharePoint 2010: Site Directories (Dynamic) &#8211; Part 3'
date: 2012-08-23T10:26:57+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=186
permalink: /2012/08/23/sharepoint-2010-site-directories-dynamic-part-3/
categories:
  - Automation
  - Script
  - Sharepoint
---
In the last two posts I covered how to create a simple site directory. Now what if you need to add a column to track something thats not gathered by the script from the object model? The previous two scripts would destroy the list during each run. In order to have persistent records in the list the script needs to update individual items.

<pre class="brush: powershell; gutter: false">#if list doesn&#039;t exist create it
$list = $null
$web = get-spweb https://webapp.domain.com
$list = $web.lists["Site List"]

if ($list -eq $null) {
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
}

#Add/update sites
$sites = Get-SPSite -WebApplication https://webapp.domain.com -limit all | where {$_ -notlike "*Office_Viewing_Service_Cache"}
foreach ($site in $sites) {
    $found = $null

    #get list
    $web = get-spweb https://webapp.domain.com
    $list = $web.lists["Site List"]
    $items = $list.items | select title

    foreach ($item in $items) {
        if ($item.Title -eq $site.URL.ToString()) {
            $found = $true
            #update item
            $SPItem = $List.Items | Where {$_["Title"] -eq $site.URL.ToString()}
            write-host "updating" $SPItem.Title.ToString()
            #update items
            $SPItem["Title"] = $site.URL
            $SPItem["Name"] = $site.RootWeb
            $SPItem["URL"] = $site.URL
            $SPItem["Owner"] = $site.Owner.displayname
            $SPItem["Secondary Contact"] = $site.SecondaryContact.DisplayName
            $SPItem.Update()
            break
        }
        elseif ($item.Title -ne $site.URL.ToString()) {
            $found = $false
        }
    }

    If ($found -eq $false) {
        #add items
        write-host "adding" $site.URL
        $newItem = $list.Items.Add()
        $newItem["Title"] = $site.URL
        $newItem["Name"] = $site.RootWeb
        $newItem["URL"] = $site.URL
        $newItem["Owner"] = $site.Owner.displayname
        $newItem["Secondary Contact"] = $site.SecondaryContact.DisplayName
        $newItem.Update()
    }
}

#Remove old items
#get list
$web = get-spweb https://webapp.domain.com
$list = $web.lists["Site List"]
$items = $list.items

foreach ($item in $items) {
    $site = $null

    $site = get-spweb $item.Title.ToString()
    if ($site -eq $null) {
        #remove item
        write-host "Removing" $item.Title.ToString()
        $item.Delete()
        $item.Update()
    }
}</pre>

The first section of the script checks to see if a custom list exists. If it doesn&#8217;t, then the script creates it. This is the same process as the last two scripts.

The next section handles updates and adds to the list. Get-SPSite returns all site collections. The main foreach loop in this section loops through each site collection. It then uses two other foreach statements to check each item thats currently in the list against the current site collection in the loop iteration. If it exists it updates the specified columns. If it doesn&#8217;t exist then it adds an entry for the site.

The second section of the script handles site deletions. It gets all entries from the directory list and checks to see if a site collection exists at the given URL. If it doesn&#8217;t it deletes the entry from the list.

Once the script has run, you can add additional columns to the list through the SharePoint web interface to track other information with each site. Since only object model data for the precreated columns is updated, additional added data will not be affected during additional runs. But new sites will continue to be added to the list and deleted sites removed. This can also be modified to work with the blog directory idea from part 2 of this series.