---
id: 369
title: 'Gnome 3 on Debian 8: How to change show hidden files setting to default to hiding hidden files'
date: 2015-05-19T08:53:17+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=369
permalink: /2015/05/19/how-to-change-show-hidden-files-setting-to-default-to-hiding-hidden-files/
categories:
  - Linux
---
I recently started playing around with Debian 8 with gnome 3 as a desktop at work and ran into an annoyance. Somehow, either by default or by some unrealized action on my part, &#8220;show hidden files&#8221; in the file manager was set to always show hidden files by default. To change the default behavior do the following.

  1. Open dconf Editor
  2. Navigate to: org.gtk.Settings.FileChooser
  3. Change &#8220;show-hidden&#8221; desired value
  4. Click &#8220;Set to Default&#8221;

I unchecked the setting and now my file manager hides all the clutter I don&#8217;t need to see under normal circumstances.