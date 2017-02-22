---
id: 37
title: '>How to uninstall an application with a script'
date: 2009-12-01T15:11:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=37
permalink: /2009/12/01/how-to-uninstall-an-application-with-a-script/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/12/how-to-uninstall-application-with.html
categories:
  - How To
  - Script
  - Windows
---
>I was just looking into doing the final remove of our old VPN software and thought I should put up a post on how to easily remove software from the command line or script. There are other ways to remove software but this is one of my favorites.

  1. Fire up regedit and navigate to hklm/software/microsoft/windows/currentversion/uninstall.
  2. Right click on the uninstall tree and click find.
  3. Type the name of the software into the search box.
  4. Under the key that is found you should find a REG\_EXPAND\_SZ value named UninstallString. The contents of of UninstallString should look something like this: MsiExec.exe /X{3D5044A5-97B8-45C0-B956-BB2376569188}

Take msiexec value and run it from the command line (there will need to be a space in between the X and { though). At this point you can probably guess that standard msiexec.exe switches apply. The nice thing about identifying the uninstaller this way is that it can easily be scripted.

Here is an example script to remove mcafee virusscan 8.7i.

> net stop McAfeeFramework  
> net stop mcshield  
> net stop mctaskmanager  
> ECHO Removing all variants of Mcafee  
> rem VirusScan Enterprise 8.7i  
> msiexec /x {147BCE03-C0F1-4C9F-8157-6A89B6D2D973} REMOVE=ALL REBOOT=R /q

Just remember to double check to make sure that ID is really the application you want to remove, otherwise you could end up with a lot of unhappy users.