---
id: 51
title: '>Using Powershell to get info out of HP ILO and OBA'
date: 2011-04-21T21:40:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=51
permalink: /2011/04/21/using-powershell-to-get-info-out-of-hp-ilo-and-oba/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/04/using-powershell-to-get-info-out-of-hp.html
categories:
  - Hardware
  - How To
  - Script
---
>I&#8217;ve been playing the the &#8220;hurry up and wait&#8221; game today and decided to teach myself something new. We have need of a central web page with links to blade chassis On Board Administrator and server ILO management pages. I got a script working to take what we had in a text file and create a list in SharePoint 2007 using powershell and the wonderful stsadm extensions, but I still need a way to auto maintain that list.

After doing some searching online (mainly this posting: <http://stackoverflow.com/questions/1819341/how-to-read-xml-file-from-web-using-powershell>) I found out that you can actually parse through some of the XML config used for ILO and OBA. I also didn&#8217;t know that ilo makes some of this information available without authentication. For example, point a web browser at an ILO interface using the following:

> http://ilo-address/xmldata?item=All

You will get an XML document with the server&#8217;s serial number, model, rack location (if set), and blade enclosure name, to name a few of the cooler things. To access that information using powershell you need to create a new xmldocument object.

> $doc = New-Object System.Xml.XmlDocument  
> $doc.Load(&#8220;http://ilo-address/xmldata?item=All&#8221;)

You can then list out the properties with the following:

> $doc.RIMP.INFRA2.BLADES.BLADE | get-member

Although viewing the xml in a web browser shows the XML in a little more friendly way. you can then display the information using a simple write-host.

> Write-Host $doc.RIMP.HSI.SPN #gets server type  
> Write-Host $doc.RIMP.HSI.SBSN #gets serial  
> Write-Host $doc.RIMP.BLADESYSTEM.BAY #chassis slot  
> Write-Host $doc.RIMP.BLADESYSTEM.MANAGER.RACK #Rack location

All together it would look like this.

> $doc = New-Object System.Xml.XmlDocument  
> $doc.Load(&#8220;http://ilo-address/xmldata?item=All&#8221;)
> 
> Write-Host $doc.RIMP.HSI.SPN #gets server type  
> Write-Host $doc.RIMP.HSI.SBSN #gets serial  
> Write-Host $doc.RIMP.BLADESYSTEM.BAY #chassis slot  
> Write-Host $doc.RIMP.BLADESYSTEM.MANAGER.RACK #Rack location

The one thing I can&#8217;t figure out is how to get the actual server name. If anyone has an idea on how to do that please leave a comment.

Alternatively you can do the same thing with the HP onboard adminstrator on C series chassis.

> $doc = New-Object System.Xml.XmlDocument  
> $doc.Load(&#8220;http://oba-address/xmldata?item=All&#8221;)
> 
> $doc.RIMP.INFRA2.BLADES.BLADE | FT -autosize

The above returns usefull things like the ILO IP for all blades in the chassis, blade serial numbers, and hardware models.

There is a lot more information in the XML configs. It was kinda fun playing around with this. Seriously though if anyone knows how to get the server name remotely from a command prompt using the ilo interface, let me know.