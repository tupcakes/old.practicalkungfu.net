---
id: 128
title: 'SharePoint 2010: Setting up the UPA to use a logon and resource forest for sync'
date: 2012-07-13T21:38:46+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=128
permalink: /2012/07/13/sharepoint-2010-setting-up-the-upa-to-use-a-logon-and-resource-forest-for-sync/
categories:
  - Sharepoint
---
This post will probably be getting some updates as time passes. The user profile service application can get complicated and temperamental. I&#8217;m sure I missed some stuff, but I&#8217;ll add updates to the post as they come up. And as always if I&#8217;ve posted something thats glaringly wrong, call me out on it. 🙂

For some reason this topic was hard to find information on. Especially relating on how to properly use the msDS-SourceObjectDN attribute in relation to SharePoint. This post is for those people who have environments that have two domains/forests (logon and resource) for either separation of data or you may be getting ready for a migration. (two or more forests setup with trusts)

**Requirements**

  * Permission: Domain Admin – to setup the permission in AD
  * Permission: Enterprise Admin – to setup the permission in AD (for netbios support)
  * Pre-populate the msDS-SourceObjectDN attribute for each user in the resource forest 
      * You can use tools like Forefront Identity Manager or powershell to set the attribute. Personal I’d recommend powershell if you don’t have FIM in place. It’s SOOO much easier in comparison.
  * One account for each AD forest your are going to sync with. Each a account should be created in their respective forest.

**AD Permissions**

In active directory users and computers right click on the root of the forest and select Properties. Click the security tab. Add the Resource forest account and grant it the Replicating Directory Changes permission.

[<img class="alignnone size-full wp-image-130" title="ADpermissions" src="http://practicalkungfu.net/wp-content/uploads/2012/07/ADpermissions.png" alt="" width="380" height="262" srcset="http://practicalkungfu.net/wp-content/uploads/2012/07/ADpermissions.png 380w, http://practicalkungfu.net/wp-content/uploads/2012/07/ADpermissions-300x206.png 300w" sizes="(max-width: 380px) 100vw, 380px" />](http://practicalkungfu.net/wp-content/uploads/2012/07/ADpermissions.png)

Do the same for the logon forest with the account for the logon domain sync connection.

*Additional permission may be needed if your netbios domain name doesn’t match your DNS name.

**Create the UPA Application and Proxy**

There are numerous scripts and walk-throughs for doing this so I’m not going to go over that much. Just create a UPA and UPA proxy per normal.

The only gotcha here is if your netbios domain name on one or both of your domains doesn’t match it’s DNS name. See the below section if you fall into that case.

**Setting up the UPA for netbios Support**

This is only required if one, or both, of your domain’s netbios name doesn’t match the DNS name. FYI, this has to be done before starting the user profile sync service.

To check to see if NetbiosDomainNames are enabled run the following powershell commands.

<pre class="brush: powershell; gutter: true">$upsa = Get-SPServiceApplication | where {$_.DisplayName -eq "User Profile Service Application"}
$upsa.NetBIOSDomainNamesEnabled</pre>

Since this is a new UPA it should return a false. Next run the following:

<pre class="brush: powershell; gutter: true">$upsa = Get-SPServiceApplication | where {$_.DisplayName -eq "User Profile Service Application"}
$upsa.NetBIOSDomainNamesEnabled=1
$upsa.Update()</pre>

If you run the commands to check the netbios status again it should return true now.

Additionally you will need to grant the sync accounts Replicating Directory Changes permissions on the Configuration container using ADSIedit.msc. This will need to be done for both forests even if the netbios condition only applies to one of your forests.

To start ADSIedit Click Start->Run and type adsiedit.msc

Right click on ADSI Edit and click Connect To…

In the pull down for “Select a well known Naming Context” choose Configuration

[<img class="alignnone size-full wp-image-131" title="ADSIsettings" src="http://practicalkungfu.net/wp-content/uploads/2012/07/ADSIsettings.png" alt="" width="380" height="291" srcset="http://practicalkungfu.net/wp-content/uploads/2012/07/ADSIsettings.png 380w, http://practicalkungfu.net/wp-content/uploads/2012/07/ADSIsettings-300x229.png 300w" sizes="(max-width: 380px) 100vw, 380px" />](http://practicalkungfu.net/wp-content/uploads/2012/07/ADSIsettings.png)

Right click on the configuration container and select properties. Click the security tab.

Add your sync account for the relevant domain and give it the Replicating Directory Changes permission just like you did in in ADUC. Do this for both forests.

**Starting the User Profile Sync Service**

Again this is something that is part of the normal UPA creation process so I’m not going to cover this in detail. The general steps are shown below.

Goto Central Admin->System Settings->Services on server

  * Select the server you want to run the sync service from.
  * Click start next to the user profile sync service
  * It will ask your for credentials to use as the service identity. Enter them and click OK.
  * Pray

**Creating the sync connections**

Here is where things get a little different. You need to setup two sync connections. One will be for pulling the display information (resource forest) for the users (displayname, firstname, manager, etc). The other is for the logon forest, this will be the account actually used by the user (what they login to their machine with).

How does this work? SharePoint actually makes a connection between the two accounts by looking at the msDS-SourceObjectDN AD attribute in the resource forest. msDS-SourceObjectDN should be populated with the DN of the logon forest’s account.

(If you’re running exchange in a logon/resource domain config chances are you are already doing something similar with the msExchMasterAccountSid to create linked mailboxes. Ask your AD and or Exchange admins, they can probably help get the msDS-SourceObjectDN set on the resource domain.)

Goto Central Admin->Application Management->Manage Service Applications->User Profile Service Application

  * Click Configure Synchronization Connections
  * Add a new connection
  * Set type to Active Directory Resource
  * Enter your forest and sync user information for the resource forest
  * Then select the OUs you want to sync.
  * Click ok

<div>
  <a href="http://practicalkungfu.net/wp-content/uploads/2012/07/resourceforestconnection.png"><img class="alignnone size-large wp-image-136" title="resourceforestconnection" src="http://practicalkungfu.net/wp-content/uploads/2012/07/resourceforestconnection-1024x355.png" alt="" width="584" height="202" srcset="http://practicalkungfu.net/wp-content/uploads/2012/07/resourceforestconnection-1024x355.png 1024w, http://practicalkungfu.net/wp-content/uploads/2012/07/resourceforestconnection-300x104.png 300w, http://practicalkungfu.net/wp-content/uploads/2012/07/resourceforestconnection-500x173.png 500w, http://practicalkungfu.net/wp-content/uploads/2012/07/resourceforestconnection.png 1327w" sizes="(max-width: 584px) 100vw, 584px" /></a>
</div>

Setting up the logon forest connection is almost the same process. Except this time use the Active Directory Logon Data connection type.

  * Set type to Active Directory Logon Data
  * Enter your forest and sync user information for the logon forest
  * Then select the OUs you want to sync.
  * Click ok

<div>
  <a href="http://practicalkungfu.net/wp-content/uploads/2012/07/logonforestconnection.png"><img class="alignnone size-large wp-image-137" title="logonforestconnection" src="http://practicalkungfu.net/wp-content/uploads/2012/07/logonforestconnection-1024x391.png" alt="" width="584" height="222" srcset="http://practicalkungfu.net/wp-content/uploads/2012/07/logonforestconnection-1024x391.png 1024w, http://practicalkungfu.net/wp-content/uploads/2012/07/logonforestconnection-300x114.png 300w, http://practicalkungfu.net/wp-content/uploads/2012/07/logonforestconnection-500x190.png 500w, http://practicalkungfu.net/wp-content/uploads/2012/07/logonforestconnection.png 1254w" sizes="(max-width: 584px) 100vw, 584px" /></a>
</div>

**Monitoring the sync progress**

At this point you should be ready to start a full sync, but it’s a good idea to watch the MIISclient to make sure the sync process works.

To start the client run: &#8220;C:\Program Files\Microsoft Office Servers\14.0\Synchronization Service\UIShell\miisclient.exe&#8221;

Now I have to say this warning. **Never change anything from inside this client. It would be bad.**

Now that the warning is out of the way you can look inside all you want, in fact the FIM client is invaluable for troubleshooting sync issues.

There are a lot of errors that can appear in here, but the one I’ve come across the most is when you get an operation status of stopped-connectivity. If you click on the job and then look in the connection status window, it should show the name of a domain controller. Click on the name of the DC and a new window will appear. It will show you the cause of the error. In this case it’s usually Replication ccess was denied, which means the replicating directory changes permission aren’t setup correctly.

[<img class="alignnone size-full wp-image-138" title="replicationaccessdenied" src="http://practicalkungfu.net/wp-content/uploads/2012/07/replicationaccessdenied.png" alt="" width="261" height="243" />](http://practicalkungfu.net/wp-content/uploads/2012/07/replicationaccessdenied.png)

&nbsp;

**Parting notes**

Beware sIDHistory:

<http://blogs.technet.com/b/rgullick/archive/2010/05/15/sharepoint-people-picker.aspx>

Apparently there is a condition that the people pick will have problems with if you use sIDHistory.I mention it now because if you have a resource and logon forest setup, you may be in the middle of a domain migration. The 10000ft view of this issue is that the people picker searches over two way trusts. It will then see the SID in the sIDHistory attribute on the stub account as well as the original logon account objectSID. This confuses and angers the people picker causing it not be able to resolve users. End result you can&#8217;t set permissions or add users to people and group fields. This could potentially impact my site creation and work flows (probably some other stuff to). You can probably get around this issue by setting the search scope of the people picker for each site collection, however this will get messy fast if you have more that one logon domain. Resolving account across multiple forests: <http://technet.microsoft.com/en-us/library/dd279546.aspx>

Update 2010-07-23: I forgot to mention [this document](http://www.microsoft.com/en-us/download/details.aspx?id=11332 "this document") as well. Page 47 also covers this topic. The document does go over the basics of getting this setup but doesn&#8217;t say much else.