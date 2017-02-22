---
id: 96
title: 'SharePoint 2010: Server out of memory in ULS log?'
date: 2012-05-23T20:27:21+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=96
permalink: /2012/05/23/sharepoint-2010-server-out-of-memory-in-uls-log/
categories:
  - Sharepoint
---
What the hell!?!?! Thats all I&#8217;ve got to say about this issue. So here&#8217;s what happened. I created a vanilla web application and it was working. I realized after a while that I forgot to set the portalsuperuseraccount and portalsuperreaderaccount properties so I set them. fast forward&#8230; I give the web app to the people that requested it and whaaa, it doesn&#8217;t work! It&#8217;s giving a correlation ID and everything. I think great I&#8217;ll just find the ULS events related to the correlation ID and get this fixed. This is what was logged:

<pre class="brush: text; gutter: true">05/23/2012 15:18:56.35 	w3wp.exe (SERVERNAME:0x2134)            	0x1D78	SharePoint Foundation         	General                       	8kh7	High	Server Out Of Memory.  There is no memory on the server to run your program. Please contact your administrator with this problem.	3afb7d29-3d00-4fff-894f-6a6d783646f7
05/23/2012 15:18:56.36 	w3wp.exe (SERVERNAME:0x2134)            	0x1D78	Web Content Management        	Publishing                    	8vzh	Unexpected	PortalSiteMapProvider was unable to fetch current node, request URL: /_layouts/settings.aspx, message: Server Out Of Memory.  There is no memory on the server to run your program. Please contact your administrator with this problem., stack trace:    at Microsoft.SharePoint.Library.SPRequestInternalClass.GetUserToken(String bstrUrl, String bstrLogin)     at Microsoft.SharePoint.Library.SPRequest.GetUserToken(String bstrUrl, String bstrLogin)     at Microsoft.SharePoint.SPWeb.GetUserToken(String userName)     at Microsoft.SharePoint.Publishing.CacheManager.&lt;.ctor&gt;b__0(SPSite newSite)     at Microsoft.SharePoint.Publishing.CommonUtilities.&lt;&gt;c__DisplayClass1.&lt;RunWithElevatedSite&gt;b__0()     at Microsoft.SharePoint.SPSecurity.&lt;&gt;c__DisplayClass4.&lt;RunWithElevatedPrivileges&gt;b__2()     at Microsoft....	3afb7d29-3d00-4fff-894f-6a6d783646f7
05/23/2012 15:18:56.36*	w3wp.exe (SERVERNAME:0x2134)            	0x1D78	Web Content Management        	Publishing                    	8vzh	Unexpected	...SharePoint.Utilities.SecurityContext.RunAsProcess(CodeToRunElevated secureCode)     at Microsoft.SharePoint.SPSecurity.RunWithElevatedPrivileges(WaitCallback secureCode, Object param)     at Microsoft.SharePoint.SPSecurity.RunWithElevatedPrivileges(CodeToRunElevated secureCode)     at Microsoft.SharePoint.Publishing.CommonUtilities.RunWithElevatedSite(SPSite siteNonElev, Boolean allowUnsafeUpdates, ElevatedSiteProcessor callWithElevatedSite)     at Microsoft.SharePoint.Publishing.CacheManager..ctor(SPSite site)     at Microsoft.SharePoint.Publishing.CacheManager.GetManager(SPSite site, Boolean useContextSite, Boolean allowContextSiteOptimization)     at Microsoft.SharePoint.Publishing.Navigation.PortalSiteMapProvider.get_ObjectFactory()     at Microsoft.SharePoint.Publishing.Navigation.Por...	3afb7d29-3d00-4fff-894f-6a6d783646f7
05/23/2012 15:18:56.36*	w3wp.exe (SERVERNAME:0x2134)            	0x1D78	Web Content Management        	Publishing                    	8vzh	Unexpected	...talSiteMapProvider.get_CurrentNode()	3afb7d29-3d00-4fff-894f-6a6d783646f7
05/23/2012 15:18:56.53 	w3wp.exe (SERVERNAME:0x2134)            	0x1D78	SharePoint Foundation         	General                       	8sl1	High	DelegateControl: Exception thrown while adding control 'Microsoft.SharePoint.Publishing.Navigation.PortalSiteMapDataSourceSwitch': Object reference not set to an instance of an object.	3afb7d29-3d00-4fff-894f-6a6d783646f7</pre>

&#8220;Server out of memory&#8221;? The WFEs in the environment have 16 GB of ram each. No way am I buying that reason. Today I remembered, this is the only web application in our farm that is setup to use windows classic authentication. I checked the portalsuperuseraccount and portalsuperreaderaccount properties and sure enough I had followed my documentation for creating a web application to the letter. Unfortunately my documentation assumes I&#8217;m creating a claims authentication based web application. DOH! I checked using:

<pre class="brush: powershell; gutter: true">$wa = get-spwebapplication webappURL
$wa.properties</pre>

Sure enough I had set the values of the properties to be the claim ID for the users. In other words they appeared with the i:0#.w| prefix. I ran the following to correctly set them.

<pre class="brush: powershell; gutter: true ">$wa = Get-SPWebApplication webappURL
$wa.properties["portalsuperuseraccount"] = "domain\svcsp-cacheuser"
$wa.properties["portalsuperreaderaccount"] = "domain\svcsp-cachereader"
$wa.update()</pre>

The change should take effect right away and not cause downtime.

In retrospect the some of the text in the events do kind of lean toward the issue being something with the publishing features. However it was hard to get past the &#8220;Server out of memory&#8221; error. I&#8217;m sure that makes some kind of sense on a technical level but it&#8217;s a very vague and misleading error. I hope this helps someone.