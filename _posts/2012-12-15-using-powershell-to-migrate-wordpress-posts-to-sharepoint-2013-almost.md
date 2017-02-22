---
id: 226
title: Using Powershell to migrate WordPress posts to SharePoint 2013 (almost)
date: 2012-12-15T23:16:01+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=226
permalink: /2012/12/15/using-powershell-to-migrate-wordpress-posts-to-sharepoint-2013-almost/
categories:
  - Script
  - Sharepoint
---
I sat down tonight and started to figure out a way to take the post export feature of WordPress (version 3.5 at the time of this posting) and use it to programmatically create posts in a SharePoint 2013 blog site. The script does work however it doesn’t migrate everything (yet?) and has one fairly major issue.

The WordPress export feature exports all the post information to XML.

[<img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border-width: 0px;" title="image" alt="image" src="http://practicalkungfu.net/wp-content/uploads/2012/12/image_thumb.png" width="563" height="297" border="0" />](http://practicalkungfu.net/wp-content/uploads/2012/12/image.png)

This is very handy since powershell does awesome with XML.  <img class="wlEmoticon wlEmoticon-smile" alt="Smile" src="http://practicalkungfu.net/wp-content/uploads/2012/12/wlEmoticon-smile.png" />You can take that XML file and read it in like so:

[xml]$posts = Get-Content posts.xml

Then get the Post list from the destination SharePoint 2013 blog site.

$URL = &#8220;[http://sp2013.tup.net&#8221;](http://sp2013.tup.net")
  
$title = &#8220;Posts&#8221;
  
$list = (Get-SPWeb -identity $URL).Lists[$Title]

Then each post in the XML is looped through and a new item is created from the values.

foreach($post in $posts.rss.channel.item)
  
{
  
$newItem = $list.Items.Add()
  
$newItem[&#8220;Title&#8221;] = $post.title
  
$newItem[&#8220;Body&#8221;] = [String]$post.encoded.InnerText
  
$pubdate = [datetime]::ParseExact($post.post_date,”yyyy-MM-dd HH:mm:ss”,$null)
  
$newItem[&#8220;Published&#8221;] = $pubdate

#not working
  
#$newItem[&#8220;Category&#8221;] =
  
#$newItem[&#8220;Comments&#8221;] =
  
$newItem.Update()
  
}

There were a couple things that threw me a bit, but I found some posts that helped (listed below). The first issue is that getting at the CDATA line in the XML is not obvious. BTW the way I found to deal with the CDATA content only seems to work in powershell 3. If you want to use this for SharePoint 2010 you’ll have to find another way to do it. Do no install powershell 3 and .net 4 on a SharePoint 2010 box. Trust me on this. I broke my 2010 dev box this way. <img class="wlEmoticon wlEmoticon-smile" alt="Smile" src="http://practicalkungfu.net/wp-content/uploads/2012/12/wlEmoticon-smile.png" />

The second gotcha is converting a string into a date object.

The full script is below. Right now it does not handle categories or comments. I may try and get those working at some point. It also doesn’t convert the post content very well (picture below showing what I’m talking about). I originally started this because I was thinking about moving my blog to a SharePoint 2013 foundation install on my home hardware. Unfortunately as of right now, 2013 is running way to slow on my home hardware. So I guess I’ll be staying on WordPress for now. (Maybe I’ll try to make the script work with SharePoint 2010 blog sites.)

[xml]$posts = Get-Content posts.xml
  
$URL = &#8220;[http://sp2013.tup.net&#8221;](http://sp2013.tup.net")
  
$title = &#8220;Posts&#8221;
  
$list = (Get-SPWeb -identity $URL).Lists[$Title]

foreach($post in $posts.rss.channel.item)
  
{
  
$newItem = $list.Items.Add()
  
$newItem[&#8220;Title&#8221;] = $post.title
  
$newItem[&#8220;Body&#8221;] = [String]$post.encoded.InnerText
  
$pubdate = [datetime]::ParseExact($post.post_date,”yyyy-MM-dd HH:mm:ss”,$null)
  
$newItem[&#8220;Published&#8221;] = $pubdate

#not working
  
#$newItem[&#8220;Category&#8221;] =
  
#$newItem[&#8220;Comments&#8221;] =
  
$newItem.Update()
  
}

Here is the result of the script run. Note the published dates.

[<img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border: 0px;" title="image" alt="image" src="http://practicalkungfu.net/wp-content/uploads/2012/12/image_thumb1.png" width="625" height="281" border="0" />](http://practicalkungfu.net/wp-content/uploads/2012/12/image1.png)

[<img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border: 0px;" title="image" alt="image" src="http://practicalkungfu.net/wp-content/uploads/2012/12/image_thumb2.png" width="625" height="413" border="0" />](http://practicalkungfu.net/wp-content/uploads/2012/12/image2.png)

And the major fail. Some of the post content is garbled.

[<img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border: 0px;" title="image" alt="image" src="http://practicalkungfu.net/wp-content/uploads/2012/12/image_thumb3.png" width="625" height="408" border="0" />](http://practicalkungfu.net/wp-content/uploads/2012/12/image3.png)

Even though there are some things to address, I thought I’d post the script anyway since it’s a pretty good example of how to use an XML file to create content in SharePoint. Also the garbled content issue may be a result of past plugins or the fact that I was originally on blogger. So you’re mileage may vary.

Date conversion (string to date object)
  
<http://techibee.com/powershell/powershell-converting-string-to-datetime-format/952>
  
gets content from powershell (the CDATA issue)
  
<http://stackoverflow.com/questions/8305622/powershell-parsing-of-cdata-section>
  
Use powershell to manipulate sharepoint lists
  
<http://blogs.technet.com/b/heyscriptingguy/archive/2010/09/22/use-powershell-to-manage-lists-views-and-items-in-sharepoint.aspx>

Update: Another thing I forgot to mention that this doesn&#8217;t do in relation to wordpress&#8230;It doesn&#8217;t migrate uploaded photos that are embedded in posts. 🙁