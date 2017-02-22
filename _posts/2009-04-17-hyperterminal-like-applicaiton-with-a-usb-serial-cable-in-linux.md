---
id: 15
title: '>Hyperterminal like applicaiton with a USB Serial Cable in linux'
date: 2009-04-17T03:09:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=15
permalink: /2009/04/17/hyperterminal-like-applicaiton-with-a-usb-serial-cable-in-linux/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/hyperterminal-like-applicaiton-with-usb.html
categories:
  - How To
  - Linux
---
>

<div>
  In order to get USB to serial converters working in linux with minicom, you must chmod give non-root users the ability to access the USB to serial device.
</div>

<div>
</div>

<div>
  Install minicom using the normal methods for your distro. Then start minicom as root.
</div>

<div>
</div>

<div>
  su &#8211;
</div>

<div>
  minicom -s
</div>

<div>
</div>

<div>
  set to 9600 8N1
</div>

<div>
  save settings and default and exit minicom.
</div>

<div>
</div>

<div>
  Next give normal users the ability to use the USB serial port.
</div>

<div>
  chmod a+rw /dev/ttyUSB0
</div>

<div>
</div>

<div>
  exit to a normal user shell and try to start minicom
</div>