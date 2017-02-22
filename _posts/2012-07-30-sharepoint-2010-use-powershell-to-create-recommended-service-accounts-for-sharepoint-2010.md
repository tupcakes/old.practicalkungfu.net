---
id: 164
title: 'SharePoint 2010: Use Powershell to create recommended service accounts for SharePoint 2010'
date: 2012-07-30T21:49:36+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=164
permalink: /2012/07/30/sharepoint-2010-use-powershell-to-create-recommended-service-accounts-for-sharepoint-2010/
categories:
  - Automation
  - Script
  - Sharepoint
---
So I was building an environment today and I thought &#8220;I don&#8217;t want to make all the service accounts by hand&#8221;. I pulled this script together and thought I&#8217;d share it.

<pre class="brush: powershell; gutter: false">Import-Module activedirectory

#creates a random 15 character complex password
Function New-Password
{
    param( 
	   [int] $len = 15,
	   [string] $chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789_!@#$%^&*()_"
    )

    $bytes = new-object "System.Byte[]" $len
    $rnd = new-object System.Security.Cryptography.RNGCryptoServiceProvider
    $rnd.GetBytes($bytes)
    $result = ""

    for( $i=0; $i -lt $len; $i++ ) {
    	$result += $chars[ $bytes[$i] % $chars.Length ]	
    }
    $result
}

#list of recomended service accounts
$users = @("svcsp-c2wts","svcsp-cachereader","svcsp-cacheuser","svcsp-crawl","svcsp-farm","svcsp-search","svcsp-serviceapps","svcsp-WA","svcsp-SQLServer","svcsp-SQLAgent","svcsp-userprofile")

#where to place the users
$OU = "&lt;DN goes here&gt;"
Write-Output "Name,Password" | Out-File .\serviceusers.csv

foreach ($user in $users) {
    $password = New-Password
    New-ADUser -Name $user -DisplayName $user -Path $OU -CannotChangePassword:$True -PasswordNeverExpires:$True -enable:$True -accountPassword (ConvertTo-SecureString -AsPlainText $password -Force) -PassThru -WhatIf:$true
    Write-Host "User: $user"
    Write-Host "Pass: $password"
    Write-Output "domain\$user,$password" | Out-File .\serviceusers.csv -Append
}

Write-Host "This script just created a file called serviceuser.csv. It can be used in the script to add the managed accounts to sharepoint."</pre>

The script does a few things. First it declares a function that will be used to generate a random and complex 15 character password. (handy if your security policy requires such things. most do.) I really wish I could take credit for this function. I got it off the internet, but I can&#8217;t remember where. If this function looks familiar I apologize for the lack of a source list. Please leave a link in the comments back to the original if anyone has seen it before.

Next the the list of users to be created is stored in the $users array. This is just the list of users I like to use for a farm. Your mileage may vary. Also at this point we tell the script where to create the new users.

The foreach loop generates a password and sets various attributes and properties that are common for service accounts, Password never expires, cannot change password, enabled, etc&#8230;

After each user is created it outputs the username and the password for each copying.

There is more I could probably do with this, like create security groups, but the situation I needed this for today didn&#8217;t really call for that. Anyway I hope someone finds it useful.

Update 2012-8-1: FYI, the script also creates service accounts for the SQL server and SQL agent services. I also changed the script so that it will create a CSV file of the account names and passwords. That CSV file can then be used to add the managed accounts into SharePoint. (Probably more on that later.)

&nbsp;