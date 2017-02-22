---
id: 33
title: '>802.1x, XP, DHCP, and group policy FAIL!'
date: 2009-09-14T21:50:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=33
permalink: /2009/09/14/802-1x-xp-dhcp-and-group-policy-fail/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/09/8021x-xp-dhcp-and-group-policy-fail.html
categories:
  - How To
  - Windows
---
>This is something I came across a while back. When using 802.1x authentication the XP winlogon service will not wait for group policy to process before proceeding. The result, in my experience, is a DHCPNACK event in the event log and complete failure of group policy application. There is a [reghack](http://support.microsoft.com/kb/840669) that will adjust the group policy wait time.

> After you apply Windows XP Service Pack 2, you must add the <strong class="uiterm">GpNetworkStartTimeoutPolicyValue</strong> registry entry. This entry defines the number of seconds to wait before trying to run the Group Policy startup script again. To find the value that will work for your configuration, define a decimal value of 60, and then increase the value until the problem is resolved. To add the registry entry and to define the value, follow these steps:
> 
>   1. Click <strong class="uiterm">Start</strong>, click <strong class="uiterm">Run</strong>, type <span class="userInput">regedit</span>, and then click <strong class="uiterm">OK</strong>.
>   2. Expand the following subkey: <div class="indent">
>       HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
>     </div>
> 
>   3. Right-click <strong class="uiterm">Winlogon</strong>, point to <strong class="uiterm">New</strong>, and then click <strong class="uiterm">DWORD Value</strong>.
>   4. To name the new entry, type <span class="userInput">GpNetworkStartTimeoutPolicyValue</span>, and then press ENTER.
>   5. Right-click <strong class="uiterm">GpNetworkStartTimeoutPolicyValue</strong>, and then click <strong class="uiterm">Modify</strong>.
>   6. Under <strong class="uiterm">Base</strong>, click <strong class="uiterm">Decimal</strong>.
>   7. In the <strong class="uiterm">Value data</strong> box, type <span class="userInput">60</span>, and then click <strong class="uiterm">OK</strong>.
>   8. Exit Registry Editor, and then restart the computer.
>   9. If the Group Policy startup script does not run, increase the value of the <strong class="uiterm">GpNetworkStartTimeoutPolicyValue</strong> registry entry.

  1. 

Honestly 60 seconds is probably a bit overkill. 15 to 30 seconds would probably be fine.