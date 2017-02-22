---
id: 249
title: Find out who is using your Exchange hub transport servers for SMTP
date: 2013-01-24T10:15:12+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=249
permalink: /2013/01/24/find-out-who-is-using-your-exchange-hub-transport-servers-for-smtp/
categories:
  - Email
  - Script
---
We started planning our eventual decom of our old exchange environment. While there is a DNS alias that applications are supposed to use for SMTP that points at the hub transports, sometimes people get it in their head to just use the server names. So if we tear down the old exchange environment their apps may suffer some email outages. We don&#8217;t want

It turns out discovering what IPs that use your hub transports for SMTP is pretty easy. Exchange provides a powershell cmdlet called Get-MessageTrackingLog. It can be used to search the tracking logs using various filters. You can also use that cmdlet to get a list of all hosts that are sending mail through exchange.

<pre class="brush: powershell; gutter: false">$log = Get-MessageTrackingLog -Server server -Resultsize unlimited | select EventId,clienthostname,clientIP | where {$_.eventid -eq "RECEIVE"}
$log | select clienthostname,clientip -Unique | sort clientip | Export-Csv -NoTypeInformation server.txt</pre>

You will then get an output file with an organized list of all IPs that are using your exchange hub transport server. Keep in mind that your own exchange server IPs may appear in this list.