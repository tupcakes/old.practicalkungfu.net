---
id: 19
title: '>Oh no, MIT is trying to keep their users safe!!!'
date: 2009-04-19T03:25:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=19
permalink: /2009/04/19/oh-no-mit-is-trying-to-keep-their-users-safe/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/oh-no-mit-is-trying-to-keep-their-users.html
categories:
  - Networking
  - Security
---
><a onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" href="http://2.bp.blogspot.com/_i4gWH_TFjMU/Ses7xY4HK3I/AAAAAAAADG8/QwiKV83FcaA/s1600-h/MITnetworkmonitoring.jpg"><img style="margin: 0pt 10px 10px 0pt; float: left; cursor: pointer; width: 246px; height: 320px;" src="http://2.bp.blogspot.com/_i4gWH_TFjMU/Ses7xY4HK3I/AAAAAAAADG8/QwiKV83FcaA/s320/MITnetworkmonitoring.jpg" alt="" id="BLOGGER_PHOTO_ID_5326416703973174130" border="0" /></a>  
Last night I saw an article on [Slashdot](http://yro.slashdot.org/article.pl?sid=09/04/18/2224226&from=rss) that caught my attention titled &#8220;MIT Tracking Campus Net Connections Since 1999.&#8221; I had a sick feeling about it so I [read deeper](http://tech.mit.edu/V129/N20/networkmonitoring.html). (Disclaimer: Rant ahead!)

Essentially what is happening here is that MIT&#8217;s IT department is preforming the extremely evil and nefarious act of running Snort on a SPAN port and gathering Netflow data. (If you didn&#8217;t detect the sarcasm in that statement, please insert it now.)

Before I go any farther I realize that the the main point here is that there was no &#8220;policy&#8221; regarding this practice. Though the tone of the article seems to be trying to infer some alternative motive on the part of the MIT IS&T department.

First let me address Netflows. Netflows are nothing more than a method to watch how much data is going through an interface. Netflows do <span style="font-weight: bold;">not</span> spy on users. I&#8217;d say more but that&#8217;s about all Netflows do! The picture is an example of a Netflow capture.

Snort on the other hand does grab the packets from the network for analysis. The easiest way to set this up is with a span port or in my world a monitor port (Procurve dork). Span ports create a copy of all the data passing through a switch and recreate it on the port designated as the span port. This is the easiest way to setup a IDS because it allows you to set it up on a network without making the IDS box a router or proxy. It can sample the network data off to the side and leave the rest of the network alone.

Snort does create a copy of the data going over a network but for a short time. It&#8217;s called an intrusion detection system because that&#8217;s what it does. It does not monitor people in the tone the article is written in.

I love this quote:

> &#8220;Undergraduate Association President Noah S. Jessop ’09 said he was surprised that IS&T was collecting this information without notice. “It is not the kind of thing I would expect from MIT, and it is definitely not the kind of thing that I would expect to hear long after the fact.”&#8221;

Mr. Jessop probably didn&#8217;t hear about it until long after the fact because this level of monitoring is common security practice in most organization on the Earth with an IT department. Honestly I&#8217;d be very surprised to find out that this is all a school the size of MIT is doing to watch for hackers and compromised systems on their network.

Before I get a bunch of hate mail for this I should say that I do agree that policies are important and that MIT should have a policy that dictates how long the snort logs are kept. Policies protect organizations and departments from internal deputes (supposed to anyway) and they provide a set of guidelines to follow which helps streamline processes.

> &#8220;Professor Harold Abelson, who teaches 6.805 “Ethics and the Law on the Electronic Frontier,” found it troubling that these logs were being collected without public knowledge. “It’s a violation of fair information practices to be keeping logs that people don’t know about … If they’re collecting logs, they have to inform people that it’s there. If they’re collecting logs, there has to be a policy on how those logs are used or not used.”&#8221;

Professor Abelson needs to realize that if you connect a computer to a network that you should assume that there are connection logs begin kept. Most off the shelf home routers keep minimal connection logs. Connection logs are kept for many reasons, most basic of which is simple troubleshooting. Companies/Universities are under no obligation to inform user about such practices, especially about something that is a common security practice.

Also:

> &#8220;About the logs, Jessop said “It’s egregious to implement measures on the network that could be used to circumvent user privacy without both policies and procedures in place and some means for the users to understand what the implications to them might be,” Jessop said.
> 
> “If you told me this was Comcast, I wouldn’t have been quite as surprised,” he said.&#8221;

My point is, in short, if you connect to a network that you do not control, always assume some level of logging/monitoring is being done as a matter of common sense. If you are really concerned about people spying (which you should be on public networks), encrypt your data! I do agree with Jessop that people should understand the implications of running their computers on public networks. Here I&#8217;ll even provide a couple links to help.  
<http://www.truecrypt.org/>  
[How to setup a ssh proxy](http://lifehacker.com/software/ssh/geek-to-live--encrypt-your-web-browsing-session-with-an-ssh-socks-proxy-237227.php)

Ok, my rant is done. It just bugs me that people always think their IT department is out to spy on them or do something evil. Believe me we are here to help protect the computer systems in our employer&#8217;s organizations, doing so is in our best interest and keeps us employed. 🙂