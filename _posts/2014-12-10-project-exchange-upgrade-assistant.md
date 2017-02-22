---
id: 333
title: 'Project: Exchange Upgrade Assistant'
date: 2014-12-10T16:00:05+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=333
permalink: /2014/12/10/project-exchange-upgrade-assistant/
categories:
  - 'C#'
  - Exchange
---
So a while back i started familiarizing myself with C#. I needed something relatively easy to do to get back into it. So I decided on an application that checks the status of exchange and the installed version. I thought it might be kinda nice to have something like that for Exchange patching so I know when a server is responding again after a reboot and if it&#8217;s reporting the correct version.

Yeah I know you can do the same thing with powershell and a ping command, but this was a learning experience for me.

The application is pretty simple. You run it on any domain joined machine in the same forest where exchange is installed. You can either click the Get Installed Version or Check status buttons to get all the exchange servers and their installed versions or also get the status respectively.

You don&#8217;t need to tell the app anything since it gets its server name and version information directly from the AD configuration.

[<img class="alignnone size-full wp-image-334" src="http://practicalkungfu.net/wp-content/uploads/2014/12/ExchangeUpgradeAssistant.png" alt="ExchangeUpgradeAssistant" width="976" height="658" srcset="http://practicalkungfu.net/wp-content/uploads/2014/12/ExchangeUpgradeAssistant.png 976w, http://practicalkungfu.net/wp-content/uploads/2014/12/ExchangeUpgradeAssistant-300x202.png 300w" sizes="(max-width: 976px) 100vw, 976px" />](http://practicalkungfu.net/wp-content/uploads/2014/12/ExchangeUpgradeAssistant.png)

Since this was a learning experience I posted this to github in case anyone wants to take a look at the code. Keep in mind I&#8217;m a noob. 🙂

[https://github.com/tupcakes/ExchangeUpgradeAssistant.git](https://github.com/tupcakes/ExchangeUpgradeAssistant.git "https://github.com/tupcakes/ExchangeUpgradeAssistant.git")

Or you can download the exe directly from the [releases](https://github.com/tupcakes/ExchangeUpgradeAssistant/releases "releases").

&nbsp;

&nbsp;