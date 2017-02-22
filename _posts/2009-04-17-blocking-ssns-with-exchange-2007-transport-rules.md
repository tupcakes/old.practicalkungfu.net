---
id: 10
title: '>Blocking SSNs with Exchange 2007 Transport rules'
date: 2009-04-17T02:06:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=10
permalink: /2009/04/17/blocking-ssns-with-exchange-2007-transport-rules/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2009/04/blocking-ssns-with-exchange-2007.html
categories:
  - Email
  - Security
---
><span style="border-collapse: collapse; font-family: Arial; font-size: 12px;">A while ago we began getting an unusual amount of phishing emails that have made it through our spam filters. Rather than try to block the spam/phish emails, I&#8217;ve decided to try and stop our users from sending sensitive information through email. 🙂</p> 

<p>
  Exchange 2007 supports server side transport rules which are great for a lot of things. The best part though is that the recognize regular expressions. Using the example of social security numbers we can setup a transport rule to look for numbers with the pattern of: ###-##-####
</p>

<p>
  To setup a rule open the Exchange Management Console and click on the Hub Transport role and then on the Transport Rules tab. Click on New Transport Rule and that will open up the rule wizard.
</p>

<p>
  </span>
</p>

<ol>
  <span style="border-collapse: collapse; font-family: Arial; font-size: 12px;"></p> 
  
  <li style="padding: 1px 0px;">
    Give the rule a name.
  </li>
  <li style="padding: 1px 0px;">
    On the conditions page of the wizard select &#8220;when the subject field or the body of the message contains text patters&#8221;.
  </li>
  <li style="padding: 1px 0px;">
    click on the words &#8220;text patters&#8221; and use the string: \d\d\d-\d\d-\d\d\d\d
  </li>
  <li style="padding: 1px 0px;">
    click ok and click next to move on to the next page of the wizard.
  </li>
  <li style="padding: 1px 0px;">
    on the actions page select the check box next to &#8220;send bounce message to sender with enhanced status code
  </li>
  <p>
    </span>
  </p>
  
  <ol>
    <span style="border-collapse: collapse; font-family: Arial; font-size: 12px;"></p> 
    
    <li style="padding: 1px 0px;">
      You can modify the bounce message to display anything you need it to.
    </li>
    <p>
      </span></ol> 
      
      <p>
        <span style="border-collapse: collapse; font-family: Arial; font-size: 12px;"></p> 
        
        <li style="padding: 1px 0px;">
          On the next screen you can add exceptions if you want, but in the case of social security numbers it would probably be best not to.
        </li>
        <p>
          </span></ol> 
          
          <p>
            <span style="border-collapse: collapse; font-family: Arial; font-size: 12px;">Thats pretty much it. Send a test message to an outside address with a fake SSN in it like 123-45-6789 and you should see a bounce message almost right away.</p> 
            
            <p>
              If you would like to test your regular expressions there is a good tester <a href="http://www.javaregex.com/test.html" rel="nofollow" style="color: #e41500; outline-style: none;" target="_blank">here</a>.<br />There is also some good information about putting together regular expressions for transport rules in exchange 2007 over at <a href="http://www.exchangeninjas.com/textpatterns" rel="nofollow" style="color: #e41500; outline-style: none;">Exchange Ninjas</a>.</span>
            </p>
            
            <p>
              <span style="border-collapse: collapse; font-family: Arial; font-size: 12px;">UPDATE:</span><br /><span style="border-collapse: collapse; font-family: Arial; font-size: 12px;">One note about this regex is that it won&#8217;t block SSN&#8217;s that are just the 9 digits, for example: 123456789. If anyone knows of a good way to block those or a better way to block SSNs in general please let me know.<br /></span>
            </p>