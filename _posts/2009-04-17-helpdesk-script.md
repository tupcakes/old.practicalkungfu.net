---
id: 12
title: '>Helpdesk script'
date: 2009-04-17T02:40:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=12
permalink: /2009/04/17/helpdesk-script/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/helpdesk-script.html
categories:
  - How To
  - Script
---
>

<div>
  asdfThis script adds the computer name to the My computer icon. This is good for helpdesks who constantly have to ask their customers, &#8220;What&#8217;s your computer name?&#8221;
</div>

<div>
</div>

<div>
</div>

<div>
</div>

> <div>
>   Const sKey = &#8220;HKEY_CLASSES_ROOTCLSID{20D04FE0-3AEA-1069-A2D8-08002B30309D}&#8221;
> </div>
> 
> <div>
>
> </div>
> 
> <div>
>   Dim oWSS,sTemp,sGarbage
> </div>
> 
> <div>
>   On Error Resume Next
> </div>
> 
> <div>
>   Set oWSS = WScript.CreateObject(&#8220;WScript.Shell&#8221;)
> </div>
> 
> <div>
>   sTemp = oWSS.RegRead(sKey & &#8220;LocalizedString&#8221;)
> </div>
> 
> <div>
>   If Err.Number = 0 Then
> </div>
> 
> <div>
>   sGarbage = oWSS.RegRead(sKey & &#8220;LocalizedStringBackup&#8221;)
> </div>
> 
> <div>
>   If Err.Number 0 Then
> </div>
> 
> <div>
>   oWSS.RegWrite sKey & &#8220;LocalizedStringBackup&#8221;, sTemp, &#8220;REG_EXPAND_SZ&#8221;
> </div>
> 
> <div>
>   End If
> </div>
> 
> <div>
>   End If
> </div>
> 
> <div>
>   oWSS.RegWrite sKey & &#8220;LocalizedString&#8221;, &#8220;My Computer %COMPUTERNAME%&#8221;,
> </div>
> 
> <div>
>   &#8220;REG_EXPAND_SZ&#8221;
> </div>
> 
> <div>
>   Set oWSS = Nothing
> </div>

<div>
</div>

<div>
  <blockquote>
  </blockquote>
  
  <blockquote>
  </blockquote>
  
  <p>
    </div> 
    
    <div>
    </div>