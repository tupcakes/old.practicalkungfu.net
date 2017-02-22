---
id: 53
title: '>Android: Auto turn on wifi while charging with Tasker'
date: 2011-04-28T01:23:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=53
permalink: /2011/04/28/android-auto-turn-on-wifi-while-charging-with-tasker/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/04/so-ive-been-playing-with-tasker-for.html
categories:
  - Automation
  - How To
  - Phones
---
>So I&#8217;ve been playing with [Tasker](https://market.android.com/details?id=net.dinglisch.android.taskerm&feature=search_result) for Android lately and let me say that this app can do amazing things. Here is a simple example. Say you want to enable wifi automatically if you have a power source plugged in.

  1. Create a new Profile named plugged in wifi
  2. Select the State context
  3. Select the Power state
  4. Click Done (should be power source = any
  5. Select new task and name power on wifi
  6. click the +
  7. Select the Net category
  8. Select the wifi action and set to On
  9. Click done until you are back a the main menu and then click apply

You&#8217;re wifi will now turn on when you have plugged into USB power or AC. An obvious problem with this simple task is that the wifi will turn on if you plug into your car charger (although that may not matter to you). Also your battery will charge a little slower.