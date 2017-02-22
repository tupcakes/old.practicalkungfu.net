---
id: 49
title: '>Why is Exchange 2010 so slow on really good Proliant G6 and G7 hardware?'
date: 2011-03-28T21:08:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=49
permalink: /2011/03/28/why-is-exchange-2010-so-slow-on-really-good-proliant-g6-and-g7-hardware/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/03/why-is-exchange-2010-so-slow-on-really.html
categories:
  - Email
  - How To
  - Windows
---
>

<a rel="lightbox[350]" href="http://karuppuswamy.com/wordpress/wp-content/uploads/2008/12/storport1.jpg"><br /></a>I&#8217;ve been working on this problem for a while now. To give a little background. We migrated from Exchange 2007 to Exchange 2010 mid to late last year. When we finally got the builk of our users on Exchange 2010, the super powerfull 12 core BL465 G6 servers with 32 GB of ram, that we were using for mailbox server (with FC connected storage) were dog slow. We spent a lot of time on the phone with Microsoft support and no progress was made for a long time. We were seeing VERY high storport latency and lots of outlook clients were having connectivity issues.

Fast forward to a week and a half ago. I was on the phone with MS support and the last thing they asked me to do was make sure the power plan was set to High Performance. I dismissed the recommendation at first not realzing the improvements MS had made in 2008 R2 in terms of power management. For those wondering the Balanced power plan does affect the processor clock speed on Server 2008 and 2008 R2!

Later that weekend I talked to a friend who told me about a similar issue that can happen to SQL servers running on Server 2008 and 2008 R2. He then informed me about a BIOS setting that they had to tweek. (thanks Ed). Since SQL and Exchange have similar behavior in terms of resource usage, I researched the issue from the point of view of SQL and sure enough there is a ton of information out there regarding SQL and processor scaling cause by power saving features.

Per my friends recomendation I grabbed a copy of CPU-Z and tried it on Exchange. Sure enough the processors on the Exchange servers we dynamically clocking down to 400MHZ with a 2x mutiplier. Way slower than a Opteron should be showing.

After more digging i found out that the Proliant G6 and G7 models have a feature enabled, in the BIOS, by default called HP Dynamic Power Savings Mode, which is not OS aware. It turns out that fixed it. Here are the BIOS settings I set on the exchange servers to get the best performance.

(FYI: The below settings turn off all power saving features of the servers, and set the processor to run at max performance. Your servers will no longer be &#8220;green&#8221;.) 🙂</p> 

<p style="margin: 0in; font-family: Calibri; font-size: 11.0pt;">
  Under HP Power Regulator, select HP Static High Performance Mode
</p>

<p style="margin: 0in;">
  <a href="http://1.bp.blogspot.com/-lqzeW0qBD8A/TZD52UT5c7I/AAAAAAAAAIE/hSuOpM7dvm4/s1600/powerregulator.png" onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}"><img style="cursor:pointer; cursor:hand;width: 320px; height: 259px;" src="http://1.bp.blogspot.com/-lqzeW0qBD8A/TZD52UT5c7I/AAAAAAAAAIE/hSuOpM7dvm4/s320/powerregulator.png" alt="" id="BLOGGER_PHOTO_ID_5589241849127203762" border="0" /></a>
</p>

<p style="margin: 0in;">
</p>

<p style="margin: 0in; font-family: Calibri; font-size: 11.0pt;">
  <p style="margin: 0in; font-family: Calibri; font-size: 11.0pt;">
    Ultra Low Power State – Disabled
  </p>
  
  <p style="margin: 0in;">
    <a href="http://4.bp.blogspot.com/-UVxtWS1L1TY/TZD6H9-SPaI/AAAAAAAAAIM/ocbGGSLDR8w/s1600/powerstate.png" onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}"><img style="cursor:pointer; cursor:hand;width: 320px; height: 306px;" src="http://4.bp.blogspot.com/-UVxtWS1L1TY/TZD6H9-SPaI/AAAAAAAAAIM/ocbGGSLDR8w/s320/powerstate.png" alt="" id="BLOGGER_PHOTO_ID_5589242152368618914" border="0" /></a>
  </p>
  
  <p style="margin: 0in;">
  </p>
  
  <p style="margin: 0in; font-family: Calibri; font-size: 11.0pt;">
    <p>
      No Power Halt State (AMD C1 Clock Ramping) – Disabled
    </p>
    
    <p style="margin: 0in;">
      <a href="http://4.bp.blogspot.com/-yc5_E3fGgKw/TZD6eLKt6BI/AAAAAAAAAIU/D0tJRfEHCD8/s1600/cstate.png" onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}"><img style="cursor:pointer; cursor:hand;width: 320px; height: 267px;" src="http://4.bp.blogspot.com/-yc5_E3fGgKw/TZD6eLKt6BI/AAAAAAAAAIU/D0tJRfEHCD8/s320/cstate.png" alt="" id="BLOGGER_PHOTO_ID_5589242533867546642" border="0" /></a>
    </p>
    
    <p style="margin: 0in;">
    </p>
    
    <p style="margin: 0in;">
      I also made sure the following were disabled. (Should be by default)
    </p>
    
    <p style="margin: 0in; font-family: Calibri; font-size: 11.0pt;">
      Enable Node Interleaving – Disabled (Default)
    </p>
    
    <p style="margin: 0in; font-family: Calibri; font-size: 11.0pt;">
      <p style="margin: 0in; font-family: Calibri; font-size: 11.0pt;">
        No Execute Page Protection – Disabled (Default)
      </p>
      
      <p style="margin: 0in;">
      </p>
      
      <p style="margin: 0in;">
        <p style="margin: 0in;">
          Interestinly if you set the BIOS to OS controlled and set Sever 2008 to a balanced power plan the CPU only clocks down to 800MHZ. I think HP&#8217;s dynamic scaling is a little too aggressive.
        </p>
        
        <p style="margin: 0in;">
          <p style="margin: 0in;">
            The G6 model was the first Proliant that the Dynamic power regulator setting was introduced, or at least set as the default. I really hope this helps some other people. Since i made these changes on the Exchange 2010 servers they have been running great.
          </p>
          
          <p style="margin: 0in;">