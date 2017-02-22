---
id: 257
title: Disable autodiscover methods in Outlook 2013
date: 2013-02-10T21:40:29+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=257
permalink: /2013/02/10/disable-autodiscover-methods-in-outlook-2013/
categories:
  - Exchange
  - Group Policy
---
There is this KB article to reference and Outlook 2007 and 2010 ADM files http://support.microsoft.com/kb/2612922. The ADM files attached to the article were greate because they gave a way to disable the different methods Outlook uses to determine autodiscover in Outlook 2007 and 2010. Outlook (generically) uses 5 methods to determine discoverer.

  1. SCP object lookup
  2. Root domain query based on your primary SMTP address
  3. Query for the AutoDiscover domain
  4. HTTP redirect
  5. SRV record query in DNS

Unfortunately Outlook 2013 is a little new and MS doesn&#8217;t have a nice precanned ADM. Thats ok because the exact same methods for autodiscover are used in 2013 as they were in previous version.

I was able to create a reg file for 2013 by exporting the key that gets created when the ADM is applied via GPO for 2010 and just changing the registry path. So instead of:

HKEY\_CURRENT\_USER\Software\Policies\Microsoft\Office\**14**.0\Outlook\AutoDiscover

I changed it to:

HKEY\_CURRENT\_USER\Software\Policies\Microsoft\Office\**15**.0\Outlook\AutoDiscover

You can change the various options from the registry file as well. They are:

&#8220;ExcludeScpLookup&#8221;=dword:00000001
  
&#8220;ExcludeHttpsRootDomain&#8221;=dword:00000000
  
&#8220;ExcludeHttpsAutoDiscoverDomain&#8221;=dword:00000000
  
&#8220;ExcludeHttpRedirect&#8221;=dword:00000000
  
&#8220;ExcludeSrvRecord&#8221;=dword:00000000

Above the SCP lookup is disabled.

I&#8217;ve attached a registry file that can be imported that will disable the SCP look up in Outlook 2013. You can change the values in the reg file to suit your needs.

[Outlook2013](http://practicalkungfu.net/wp-content/uploads/2013/02/Outlook2013.zip)