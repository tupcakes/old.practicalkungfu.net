---
id: 113
title: ForeFront Protection 2010 for SharePoint (Part 2) – Automating Forefront for SharePoint config deployment with Powershell
date: 2012-07-11T16:26:13+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=113
permalink: /2012/07/11/forefront-protection-2010-for-sharepoint-part-2-automating-forefront-for-sharepoint-config-deployment-with-powershell/
categories:
  - Automation
  - Script
  - Sharepoint
---
I decided to make this a two part post since I really wasn&#8217;t happy with the basic script I wrote in the first place. I&#8217;ve streamlined it by adding a WFE server list as an array and added some powershell remoting as well as a method for remote Forefront configuration deployment without Forefront Protection Server Management Console 2010.

First if you don&#8217;t know how enabling poweshell remoting is pretty easy for a basic config. To enable PS remoting on Server 2008 R2, just run the following command from an elevated command prompt:

<pre class="brush: powershell; gutter: true">Enable-PSRemoting -Force</pre>

You will need to do this on all your WFEs.

The previous version of the script was really way to basic. It had to be run from a the same WFE every time because of the way robocopy was used in it. It also didn&#8217;t deploy the config. This isn&#8217;t so much of an issue if you want to use Forefront Protection Server Management Console 2010 for deployment. The console does allow you to select which parts of the config you want to deploy, however the config on all WFEs should be the same under normal circumstances so I personally don&#8217;t get much out of that.

I changed the $filename variable to include the computer name the config change was created on. I can&#8217;t see wanting it right now, but thought it would be nice to have. Just after that I created an array of all the server names. The $servers variable is the one you should change and put your SharePoint WFE servers into.

I also replaced the robocopy commands with a single robocopy in a for each loop that loops through the $servers array. I also used $env:computername for the source server so it won&#8217;t matter which server you make your config changes on.

The last foreach loop also loops through the $servers array. This is where the powershell remoting comes in. It creates a session for each server in the array, then runs a scriptblock that adds the FSSPSnapin for the remote session and then imports the Forefront config that we just created. The loop then kills the session.

<pre class="brush: powershell; gutter: true">##this script will export the configuration for Forefront Protection for Sharepoint 2010.
#the exported file is datestamped and then the entire directory is synced back to all
#WFEs.

Add-PSSnapin FSSPSSnapin

#varialbles
$today = Get-Date -format "yyyy-MM-dd-HH.mm.ss"
$filename = $env:computername + "-" + $today + ".xml"
$filepath = "E:\FFConfig\$filename"
$servers = @("WFE01","WFE02","WFE03","WFE04")

#Export standard and extended options
Export-FsspSettings -path $filepath

Foreach ($server in $servers) {
    robocopy \\$env:computername\e$\FFConfig \\$server\e$\FFConfig /MIR /FFT /Z /XA:H /W:5 /r:2
}

foreach ($server in $servers) {
    $session = New-PSSession -ComputerName $server
    Invoke-Command -Session $session -ScriptBlock {
	param($file)
        Add-PSSnapin FSSPSSnapin
        Import-FsspSettings -path $file
    } -ArgumentList $filepath

    Get-PSSession | ForEach-Object {Remove-PSSession $_}
}</pre>

So what I ended up with is a versioning and deployment script that doesn&#8217;t rely on Forefront Protection Server Management Console 2010. I was not impresses with the console&#8217;s method of deployment and lack of versioning so I will probably end up using this script for config deployment. Granted I&#8217;ll keep the management console since it does allow for central quarantine management which is very handy.

Update: Changed a couple things in the script that I found out weren&#8217;t working correctly.