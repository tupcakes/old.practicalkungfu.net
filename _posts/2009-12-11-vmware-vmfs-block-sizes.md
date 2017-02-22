---
id: 41
title: '>VMWare VMFS Block Sizes'
date: 2009-12-11T20:58:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=41
permalink: /2009/12/11/vmware-vmfs-block-sizes/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/12/vmware-vmfs-block-sizes.html
categories:
  - Virtualization
---
>Was just getting ready to add some more storage and thought I&#8217;d share this little table I found. As many of you know the block size you use when creating the VMFS will determine how big of files you can put in that storage area. [This site](http://itknowledgeexchange.techtarget.com/virtualization-pro/choosing-a-block-size-when-creating-vmfs-datastores/) has a nice little reference table.

• 1MB block size – 256GB maximum file size  
• 2MB block size – 512GB maximum file size  
• 4MB block size – 1024GB maximum file size  
• 8MB block size – 2048GB maximum file size

VMware seems to have a 2TB limit for VMFS which also applies to raw device mappings ([outlined here](http://www.google.com/url?sa=t&#038;source=web&#038;ct=res&#038;cd=3&#038;ved=0CBAQFjAC&#038;url=http%3A%2F%2Fwww.vmware.com%2Fpdf%2Fvsphere4%2Fr40%2Fvsp_40_config_max.pdf&#038;ei=eq0iS-TmDoaoMNPP_ekJ&#038;usg=AFQjCNGMuViOCJP119G0RikSs6pCkS8D_A&#038;sig2=uQ4yGBmzU3Om57Ppi1XIhg)). So unless you want to use extents (god help you) you&#8217;re stuck with 2TB virtual disks.