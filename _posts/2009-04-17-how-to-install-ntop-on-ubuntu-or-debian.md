---
id: 14
title: '>How to install NTOP on Ubuntu or Debian'
date: 2009-04-17T03:03:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=14
permalink: /2009/04/17/how-to-install-ntop-on-ubuntu-or-debian/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/how-to-install-ntop-on-ubuntu-or-debian.html
categories:
  - How To
  - Linux
  - Networking
---
>

<div>
  <b>Installation</b>
</div>

<div>
</div>

<div>
  sudo aptitude install ntop
</div>

<div>
  sudo ntop -A
</div>

<div>
  sudo /etc/init.d/ntop start
</div>

<div>
</div>

<div>
  Now point your web browser at your server&#8217;s dns name (or IP) on port 3000.
</div>

<div>
</div>

<div>
  <b>Using ntop on a monitor port</b>
</div>

<div>
</div>

<div>
  Use the -o option to prevent NTOP from getting confused and shutting down if it sees it&#8217;s own mac address on the monitor port.
</div>

<div>
</div>

<div>
  Start ntop with the following options
</div>

<div>
</div>

<div>
  sudo ntop -u ntop -o -d
</div>

<div>
</div>

<div>
  -u ntop will start the process under the ntop user.
</div>

<div>
  -o starts ntop with no mac address. This options is the key for running on a monitor port.
</div>

<div>
  -d starts ntop as a daemon so you can do other things on your system.</p> 
  
  <p>
    To make ntop start at boot insert this into the file /etc/rc.local.
  </p>
  
  <blockquote>
  </blockquote>
  
  <p>
    ntop -u ntop -o -d</div>