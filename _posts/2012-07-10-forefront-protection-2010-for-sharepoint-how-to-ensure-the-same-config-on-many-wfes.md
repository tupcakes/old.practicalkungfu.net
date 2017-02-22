---
id: 108
title: 'ForeFront Protection 2010 for SharePoint (Part 1) &#8211; How to ensure the same config on many WFEs'
date: 2012-07-10T20:57:18+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=108
permalink: /2012/07/10/forefront-protection-2010-for-sharepoint-how-to-ensure-the-same-config-on-many-wfes/
categories:
  - Automation
  - Script
  - Sharepoint
---
So this is a bit of a work in progress, but if you have a internet facing SharePoint environment, chances are you have some kind of WFE based AV scanning. We decided to go with ForeFront which is what this article will be about.

Say you have a fairly typical SharePoint farm but you have 2 (or more) web front ends that are load balanced. You&#8217;ve installed ForeFront on all your WFEs, and are running with pretty much default settings.

FF (ForeFront) as with most other modern MS products has some powershell support. You can add the FF snapin with the following:

<pre class="brush: powershell; gutter: true">Add-PSSnapin FSSPSSnapin</pre>

Among the cmdlets provided is one called Export-PsspSettings. This will export the current FF config from the local server to an XML file. For example:

<pre class="brush: powershell; gutter: true">Export-FsspSettings -path E:\FFConfig\filename.xml</pre>

You can then use a similar command to import that same config into FF. If you only have two WFEs I&#8217;d recomend just using the import cmdlet to distribute your config. If you get to having 3 or more WFEs, you may want to look at using Forefront Protection Server Management Console 2010. It can save a lot of busy work by allowing you to create deployment jobs.

<pre class="brush: powershell; gutter: true">Import-FsspSettings -path E:\FFConfig\filename.XML</pre>

From a technical standpoint that works. However what if you want to keep historical config changes just in case you need to revert to a previous config and push the XML config files to the other servers?

<pre class="brush: powershell; gutter: true">Add-PSSnapin FSSPSSnapin

#Create file name with date stamp
$today = Get-Date -format "yyyy-MM-dd-HH.mm.ss"
$filename = "FFconfig-" + $today + ".xml"

#Export standard and extended options
Export-FsspSettings -path E:\FFConfig\$filename

robocopy \\SP2010WFE\e$\FFConfig \\FFMCSERVER\e$\FFConfig /MIR /FFT /Z /XA:H /W:5 /r:2</pre>

The above script is pretty simple but it can save a lot of pain from a bad config change since it will allow you to keep historical config backups. Essentially it just builds a date string and makes it part of the file name used by the export command. Since it&#8217;s using the date down to the second you should never get the same file name twice. The entire directory containing the configs is then synced to a central location or your other WFEs.

In the example I&#8217;m sending the configs to the Forefront Protection Server Management Console 2010. From there I can setup a package and then a deployment job to push the config to the remaining WFEs. The management console will even let me only push parts of the config like notification or quarantine settings.

A little off the topic of SharePoint, but Forefront for Exchange also uses similar export import cmdlets. This small script could also be adapted for the Exchange version as well.

&nbsp;