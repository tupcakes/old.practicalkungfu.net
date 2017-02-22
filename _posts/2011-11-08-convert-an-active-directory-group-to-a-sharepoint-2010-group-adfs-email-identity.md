---
id: 57
title: Convert an Active Directory group to a SharePoint 2010 Group (ADFS email identity)
date: 2011-11-08T17:41:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=57
permalink: /2011/11/08/convert-an-active-directory-group-to-a-sharepoint-2010-group-adfs-email-identity/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/11/convert-active-directory-group-to.html
categories:
  - Script
  - Sharepoint
---
During a recent Sharepoint migration I had need to convert an AD group into a SharePoint group in SP 2010.

<pre class="brush: powershell; gutter: false">import-module activedirectory
add-pssnapin Microsoft.SharePoint.PowerShell
$users = Get-ADGroupMember -identity Groupname.Members | get-aduser -Properties mail | select-object mail
foreach ($user in $users) {
    $email = $user.mail.ToString()
    New-SPUser -UserAlias "i:05.t|adfs20server|$email" -Web "https://webapp/sites/site/"
    Set-SPUser -identity "i:05.t|adfs20server|$email" -Web "https://webapp/sites/site/" -Group "Site Members"
}</pre>

Essentially it creates an object that contains the group members. Then loops through each of them and creates an identity on the site collection (using the person&#8217;s email address) then adds that user to the group.

This script was created to also populate the email identity for using claims auth with ADFS, however it could also be moddified to work with gold old fashioned NTLM using classic or claims authentication.

2012-07-17 update: Time to update this post. We switched to Windows Claims auth and now I have a new script for doing the same thing in with windows claims identities. This is actually a complilation of a couple different sources. Source URLs in the comments (get-spscripts.com. I did the windows claims version of this script a little differently. This one will actually take an AD group and duplicate it as a SharePoint group, plus it will then give that group the designated permissions at the specified URL.

<pre class="brush: powershell; gutter: false">import-module activedirectory
add-pssnapin Microsoft.SharePoint.PowerShell


$ADgroup = "adgroupname" #set
$domain = "domain" #set
$SPgroup = "spgroupname" #set
$webURL = "http://sp.domain.local" #set

#uncomment the desired permission
#$permission = "Full Control"
#$permission = "Designer"
$permission = "Contribute"
#$permission = "Read"

$web = Get-SPWeb $webURL

#create group
#http://get-spscripts.com/2010/07/creating-multiple-sharepoint-groups-and.html
$newGroup = $web.SiteGroups.Add($SPgroup, $web.Site.Owner, $null, $null)
$Group = $Web.SiteGroups[$SPgroup]
$Group.AllowMembersEditMembership = $true #set
$Group.Update()

#add group to web with permissions
#http://get-spscripts.com/2010/07/adding-groups-with-permission-levels-to.html
$Group = $Web.SiteGroups[$SPgroup]
$assignment = New-Object Microsoft.SharePoint.SPRoleAssignment($group)
$role = $web.RoleDefinitions[$permission]
$assignment.RoleDefinitionBindings.Add($role);
$web.RoleAssignments.Add($assignment)

$users = Get-ADGroupMember -identity $ADgroup -Server $domain -Recursive | get-aduser | select-object samaccountname

$users | ForEach-Object {
    $user = "i:0#.w|" + $domain + "\" + $_.samaccountname
    $group.Users.Add($user, "", "", "")	
}</pre>

&nbsp;