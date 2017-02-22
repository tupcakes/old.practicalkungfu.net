---
id: 173
title: Get location and management information for a HP server using powershell (I shall call it Frankenscript!)
date: 2012-08-08T14:31:14+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=173
permalink: /2012/08/08/get-location-and-management-information-for-a-hp-server-using-powershell-i-shall-call-it-frankenscript/
categories:
  - Hardware
  - Script
  - Windows
---
So this is probably the most cobbled together thing I&#8217;ve ever done. A long time ago I did a post on how to access OAB and ILO information, through an exposed XML file, using powershell. While cool, it was missing some info, like being able to tell the name of a server thats in a slot. Today I decided to revisit the subject from the opposite direction. Give a server name and see what comes out. Here is what I ended up with. (for this to work ILO and OAB must be setup.)

<pre class="brush: powershell; gutter: false">$server = "servername"

$chassis = (Get-WmiObject HP_BladeEnclosureCS -computername $server -namespace root\HPQ).ManagementIPAddress
$chassisaddress = "http://" + $chassis + "/xmldata?item=All"
$doc = New-Object System.Xml.XmlDocument
$doc.Load($chassisaddress)

$Chassisinfo = @()

$obj = New-Object System.Object
$obj | Add-Member -type NoteProperty -Name Name -Value $server
$obj | Add-Member -type NoteProperty -Name Model -Value (get-wmiobject -computername $server win32_computersystem).Model
$obj | Add-Member -type NoteProperty -Name ServerSerial -Value (Get-WmiObject -computername $server win32_Bios).SerialNumber
$obj | Add-Member -type NoteProperty -Name ServerIP -Value (Test-Connection $server -Count 1).IPV4Address.IPAddressToString
$obj | Add-Member -type NoteProperty -Name ChassisName -Value (Get-WmiObject HP_BladeEnclosureCS -computername $server -namespace root\HPQ).Name
$obj | Add-Member -type NoteProperty -Name ChassisSlot -Value (Get-WmiObject HP_BladeCSLocation -computername $server -namespace root\HPQ).PhysicalPosition
$obj | Add-Member -type NoteProperty -Name ChassisRack -Value $doc.RIMP.INFRA2.RACK #rack location
$obj | Add-Member -type NoteProperty -Name ChassisMgmtURL -Value (Get-WmiObject HP_BladeEnclosureCS -computername $server -namespace root\HPQ).ManagementURL
$obj | Add-Member -type NoteProperty -Name ChassisIP -Value (Get-WmiObject HP_BladeEnclosureCS -computername $server -namespace root\HPQ).ManagementIPAddress
$obj | Add-Member -type NoteProperty -Name ChassisSerial -Value (Get-WmiObject HP_BladeEnclosureCS -computername $server -namespace root\HPQ).SerialNumber
$Chassisinfo += $obj

$Chassisinfo</pre>

So most of this is wmi calls to the root\HPQ namespace where all things HP live. I&#8217;m also using Test-Connection to output the server IP. And I&#8217;m using the previous XML method I mentioned before to get the chassis rack location (because apparently the blade slot is available through wmi but not the rack location&#8230;). The output should be a nice report with the following information.

<pre class="brush: text; gutter: false">Name           : servername
Model          : ProLiant BL465c G7
ServerSerial   : 2394jsdf23
ServerIP       : 10.0.0.52
ChassisName    : Chassis42
ChassisSlot    : 14
ChassisRack    : 19E
ChassisMgmtURL : http://Chassis42/index.html
ChassisIP      : 10.1.0.52
ChassisSerial  : USE3487294</pre>

The information above is fake but it should five you an idea of what the output should look like.

Update: Ok so after trying this script with a few more servers, it becomes apparent that it only works with specific setups. I wanted something more universal. Oh well, back to the drawing board. 🙂