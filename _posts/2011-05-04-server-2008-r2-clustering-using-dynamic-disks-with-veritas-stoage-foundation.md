---
id: 55
title: '>Server 2008 R2 Clustering using Dynamic disks with Veritas Stoage Foundation'
date: 2011-05-04T15:25:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=55
permalink: /2011/05/04/server-2008-r2-clustering-using-dynamic-disks-with-veritas-stoage-foundation/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/05/server-2008-r2-clustering-using-dynamic.html
categories:
  - Clustering
  - How To
  - Windows
---
>Ok, so after a lot of banging my head against this I finally asked one of our storage guys (thanks John). I was trying to setup a Windows 2008 R2 cluster for use as a SQL 2008 R2 cluster. However the SAN storage I was given was created as dynamic partitions which isn&#8217;t natively supported in Windows clustering, but is supported using Veritas Storage Foundation.

No matter how many times you try and add a new disk to the cluster it will tell you, &#8220;No disks suitable for cluster disks were found. For diagnostic information about disks available to the cluster, use the Validate a Configuration Wizard to run Storage tests.&#8221; But the validation test won&#8217;t show usable storage.

First you need to make sure that the Cluster Option for Microsoft Cluster Service (MSCS)/Failover Cluster is installed in Veritas Storage Foundation. You will need a license for this feature otherwise the check box will be greyed out. The feature can be added using Programs and Features in the control panel.

To actually make the cluster see the storage you first need to create an Empty Service or Application in the Failover Cluster Manager.

  1. Right click on Services and application
  2. Select more actions
  3. Select Create Empty Service or Application

Next you must add a Volume Manager Disk Group to the empty application you just created.

  1. Right click on the clustered application you just created.
  2. Select Add a resource
  3. Select More Resources
  4. select 9 &#8211; Add Volume Manager Disk Group

Now you have to tell the new disk group you created what the name of the disk group is in Veritas.

  1. Right click on the disk group you just created and select properties.
  2. Click the properties tab.
  3. On the line named &#8220;DiskGroupName&#8221; Type the name of the disk group, as it appear in Veritas into the Value field.
  4. Click ok.

You should probably rename the application and resource you just created so they are easily identifiable later.

For me this didn&#8217;t work right away due to some sort of locking that was happening on one of the nodes. A reboot fixed that issue.

Update: 2010/05/06 &#8211; Started having some problems when bringing the Volume Manager Disk Group online. The act of bringing the resource online seems to crash the cluster! I&#8217;m currently talking with our storage team about the issue. We have identified a FUGLY work around. Put the disk group in a deported state. Then right after creating the Volume Manager Disk Group cluster resource, detach it from the cluster application so it&#8217;s in available storage. Then you can bring it online&#8230;eventually. By eventually I mean that it seems to be very picky about when you enter the DiskGroupName.

We are still trying to figure out what&#8217;s going on with this and will probably be contacting Symantec support on the issue due to the cluster crashing implication.

Update 2012/06/13 &#8211; I know it&#8217;s been a while but I noticed I never brought this post to a conclusion. (my bad). This document was shown to me that contains all you will need to know to make Veritas Storage Foundation work with 2008 R2 clustering. [http://sfdoccentral.symantec.com/sf/5.1SP2/windows/pdf/SFW\_MSCS\_SQL2008\_Solutions\_51SP2.pdf](http://sfdoccentral.symantec.com/sf/5.1SP2/windows/pdf/SFW_MSCS_SQL2008_Solutions_51SP2.pdf)