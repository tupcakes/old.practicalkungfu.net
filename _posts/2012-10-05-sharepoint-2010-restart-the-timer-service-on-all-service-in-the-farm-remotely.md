---
id: 201
title: 'SharePoint 2010: Restart the timer service on all service in the farm remotely'
date: 2012-10-05T11:30:56+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=201
permalink: /2012/10/05/sharepoint-2010-restart-the-timer-service-on-all-service-in-the-farm-remotely/
categories:
  - Automation
  - Script
  - Sharepoint
---
Even though I don&#8217;t have to do this often, I still got sick of having to RDP or connect services.msc to each server in the farm and restart the timer service. So I found a script from <http://chrisfederico.wordpress.com/2008/01/23/using-powershell-to-restart-services/> and tied in some get-spserver fun to programatically get and restart the timer service on each farm server.

<pre class="brush: powershell; gutter: false">[void] ([Reflection.Assembly]::LoadWithPartialName("System.ServiceProcess"))

$servers = Get-SPServer | Where {$_.Role -ne "Invalid" }
#or
#[array]$servers = "Server1","Server2","Server3","Server4"
foreach ($server in $servers) {
    $service = [System.ServiceProcess.ServiceController]::GetServices($server.name) | where-object {$_.Name -eq "SPTimerV4"}

    Write-host "Stopping:" $server.name
    #stop the service
    $service.Stop()
    $service.WaitForStatus("Stopped")

    Write-host "Starting:" $server.name
    #start the service
    $service.Start()
    
}</pre>

I also left in an array that can also be used if you don&#8217;t want to use get-spserver. Just uncomment the array line and comment out the get-spservers line.