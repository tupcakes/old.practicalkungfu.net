---
id: 47
title: '>Using powershell to create a report of all GPOs'
date: 2010-10-13T22:06:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=47
permalink: /2010/10/13/using-powershell-to-create-a-report-of-all-gpos/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2010/10/using-powershell-to-create-report-of.html
categories:
  - Group Policy
  - Script
  - Sharepoint
  - Windows
---
>I was recently tasked with creating a place in Sharepoint where people can go to see what the current GPOs are and what settings are in them. This turns out to be really easy, even the Sharepoint part.

Essentially we need to get the GUID for each GPO and pipe that to the get-GPOreport cmdlet. The HTML file can then be generated based on the GPO display name.

get-gpo -all -domain domain.local | Foreach { get-gporeport -guid $\_.ID -domain domain.local -report html -path C:\\gpos\\$($\_.Displayname).html}

The above command can be put into a powershell script. Another command can be used to copy the contents of the c:\gpos location to a sharepoint document library, or just moddify the command to place the HTML directly in the library.

You can access sharepoint document libraries via explorer by clicking Start->Run and typing for example: \\sharepointURL\doclibrary