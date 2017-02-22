---
id: 240
title: 'Updated: Migrating a user from one username to another'
date: 2013-01-17T09:44:05+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=240
permalink: /2013/01/17/updated-migrating-a-user-from-one-username-to-another/
categories:
  - Sharepoint
---
Previously I posted about [http://practicalkungfu.net/2012/03/30/sharepoint-2010-changing-from-saml-claims-to-windows-claims](http://practicalkungfu.net/2012/03/30/sharepoint-2010-changing-from-saml-claims-to-windows-claims "SharePoint 2010 Changing from ADFS Claims to Windows Claims"). While that script did work for my purposes it was not streamlined and didn&#8217;t do a lot of error checking and just generally wasn&#8217;t that clean. It was more like a battering ram. The actual act of using the move-spuser cmdlet is really pretty simple.

  1. Define the old userID.
  2. Define the new userID.
  3. Get-Spweb for a site where you know the user object exists.
  4. Create a SPUser object to feed into the move-SPuser cmdlet.
  5. If a SPuser object was found confirm the new userID and call move-spuser.

<pre class="brush: powershell; gutter: false">#initialize variables
$web = $null
$user = $null
$newuserID = $null
$OlduserID = $null

#Change IDs as needed
$OlduserID = "i:0#.w|olddomain\oldusername"
$newuserID = "i:0#.w|newdomain\newusername"

$web = Get-SPWeb "https://teams.contoso.com"

$user = Get-SPUser -Web $web -Identity $OlduserID #-ErrorAction SilentlyContinue

If($user) {
    $newuserID = $web.EnsureUser("$newuserID")
    Move-SPUser -Identity $user -NewAlias $NewuserID -Confirm:$false -IgnoreSID
}</pre>

Once Move-SPUser is called it will change all instances of oldusername to newusername. Also if you plan to do a few of these kinds of moves in a row (or do a mass migration) its always a good idea to null out your variables each time (trust me on this).

While this is much cleaner in terms of how to change a users identity, it only does it for one user. My next project is to rewrite my previous script with a cleaner process for doing a mass domain migration.