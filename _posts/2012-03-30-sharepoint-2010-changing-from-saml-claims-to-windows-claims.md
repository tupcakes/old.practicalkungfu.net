---
id: 63
title: SharePoint 2010 Changing from ADFS Claims to Windows Claims
date: 2012-03-30T18:27:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=63
permalink: /2012/03/30/sharepoint-2010-changing-from-saml-claims-to-windows-claims/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2012/03/sharepoint-2010-changing-from-saml.html
categories:
  - Script
  - Sharepoint
---
So here is a scenario. You have two domains (or more), you are trying to go through a domain consolidation onto one domain. So you have multiple user domains, an another (main) domain with enabled or disabled user accounts (stub accounts) that correspond to the users out in the user domains. Your Sharepoint 2010 environment exists in this &#8220;main&#8221; domain also. because of this scenario you configured SharePoint to use ADFS claims only (no windows authentication for users)

Summarry:
  
Domain1 &#8211; main domain, contains stub accounts and the SharePoint 2010 environment.
  
Domain2 &#8211; legacy user domains, contains user accounts actually used by the end users.

SharePoint 2010 &#8211; setup to use only ADFS claims for user authentication. (No windows auth for users)

It works, however after we had implemented it we found the following article. <http://technet.microsoft.com/en-us/library/hh706161.aspx>

Toward the bottom is a list of things that don&#8217;t work, or have limited functionality with SAML authentication (ADFS claims) in SharePoint 2010. A few examples are below.

  * <span style="font-family: inherit; text-align: left;">Search Alerts</span>
  * <span style="font-family: inherit; text-align: left;">SharePoint Server 2010 Explorer View</span>
  * <span style="font-family: inherit; text-align: left;">Claims to Windows Token Service (C2WTS) &#8211; Not sure if this is just a statement that the service can&#8217;t run under a claims identity or that it just can&#8217;t do it&#8217;s job with SAML claims in use.</span>
  * <span style="font-family: inherit; text-align: left;">InfoPath Forms Services</span>
  * <span style="font-family: inherit; text-align: left;">Search crawling &#8211; does work you just have to extend every web application that uses ADFS claims, and set the extension to use NTLM claims. the crawler doesn&#8217;t support using trusted identity providers.</span>
  * <span style="font-family: inherit; text-align: left;">Audiences &#8211; can only work with user profile properties. No ad group based audiences.</span>
  * <span style="text-align: left;"><span style="font-family: inherit;">mail enabled lists/libraries &#8211; they work however you must set any lists/libraries to accept mail from all users. It can&#8217;t security filter with ADFS claims. Also email that received by a list/library will get stamped with the metadata of the windows claims identity of the sending user, not the ADFS claim identity. So metadata in mail enabled lists/libraries may not match up with the profiles in the user profile service.</span></span>
  * <span style="font-family: inherit;"><span style="text-align: left;">Secure store service &#8211; </span>When you are using the Secure Store Service, SAML claims are not translated to Windows tokens, so other services will not detect the SAML identity; the identity will be the service account, an anonymous account, or an unattended account.</span>
  * <span style="font-family: inherit;">Information rights management &#8211; also works but requires a hotfox and some extra effort to make with with SAML claims.</span>

<div style="text-align: left;">
  <span style="font-family: 'Segoe UI', Verdana, Arial;">We also had problems with a number of 3rd party applications and web parts that either needed to authenticate to SharePoint or that had to pass through the authentication from SharePoint. </span>
</div>

<div style="text-align: left;">
  <span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
</div>

<div style="text-align: left;">
  <span style="font-family: 'Segoe UI', Verdana, Arial;">Because of these limitations, and some others annoyances we started investigating switching authentication methods for the impacted web applications. Amazingly enough this wasn&#8217;t as bad as I thought. thanks to the Move-SPUser cmdlet.</span>
</div>

<div style="text-align: left;">
  <span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
</div>

<div style="text-align: left;">
  <strong><span style="font-family: 'Segoe UI', Verdana, Arial;">DISCLAIMER: I&#8217;ve run through this in our test environment and everything worked pretty well. I still don&#8217;t know how to preserve the user profiles since i&#8217;ll have to redo the UPS sync connection. ALWAYS run things like this in a </span><span style="font-family: 'Segoe UI', Verdana, Arial;">separate</span><span style="font-family: 'Segoe UI', Verdana, Arial;"> test/dev environment to make sure they work for you before running in a production environment. The risk with making a bulk change like this is pretty high since it could break all permissions in the environment. Also take content database backups before running this. (Just in case). 🙂</span></strong>
</div>

<div style="text-align: left;">
  <span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
</div>

<div style="text-align: left;">
  <span style="font-family: 'Segoe UI', Verdana, Arial;">I was able to come up with the following script. (note the comment at the begining of the script about granting rights to the UPS application. That step is important if you want to update the user profile when the move-spuser is run!)</span>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  <pre class="brush: powershell; gutter: true">#Converts all users identities to windows claims
#Pre-tasks: Grant the account, you are running this script as, full control rights in both the
#administrators list and the permissions list for the User Profile Service Application.

#Test site
$allwebs = get-SPSite -WebApplication https://site.company.com -Limit ALL | get-SPweb -Limit ALL

$claimprefix = "i:0#.w|"

foreach ($web in $allwebs) {
	foreach ($user in $web.AllUsers) {
		#exclution list: superreader, superwriter, all authenticated users context, etc (add your exclusions as needed.)
		If ($user.UserLogin -Notlike "$claimprefix*" -and $user.Email -ne "" -and $user.UserLogin -ne "SHAREPOINT\system" -and $user.UserLogin -ne "NT AUTHORITY\LOCAL SERVICE" -and $user.UserLogin -ne "NT AUTHORITY\NETWORK SERVICE" -and $user.UserLogin -ne "c:0(.s|true") {
			#find user in domain
			##Save AD user info
            $OlduserID = $null ##added to clear last result
			$OlduserID = Get-ADUser -Filter {mail -like $user.Email} -Properties mail,msDS-SourceObjectDN

			$sourceDN = $null ##added to clear last result
			$sourceDN = $OlduserID | select msDS-SourceObjectDN

			#domain2. use their OU name to determine their home domain
			If ($sourceDN -match "DC=domain2,DC=net") {
				$domain = "domain2\"
				#create windows identity based on domain\username
				$newuserID = $null
				$NewuserID = $claimprefix + $domain + $OlduserID.samaccountname.ToString()

				Move-SPUser -Identity $user -NewAlias $NewuserID -Confirm:$false -IgnoreSID
				Write-Host "Moving:" $user.UserLogin "to" $NewuserID "on subweb" $web.URL
			}

			#domain3. use their OU name to determine their home domain - add domains as needed
			If ($sourceDN -match "DC=domain3,DC=com") {
				$domain = "domain3\"
				#create windows identity based on domain\username
				$newuserID = $null
				$NewuserID = $claimprefix + $domain + $OlduserID.samaccountname.ToString()

				Move-SPUser -Identity $user -NewAlias $NewuserID -Confirm:$false -IgnoreSID
				Write-Host "Moving:" $user.UserLogin "to" $NewuserID "on subweb" $web.URL
			}
		}
	}
}</pre>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  <span style="font-family: 'Segoe UI', Verdana, Arial;">Essentially this script gets all subweb objects in a web application and cycles through them. For ever user in every subweb, it will lookup the user&#8217;s AD object based on their mail attribute (also stored in SharePoint), and returns their msDS-SourceObjectDN and attributes. The msDS-SourceObjectDN attribute can be used to determine the users home domain which is done in the two IF statements. If the msDS-SourceObjectDN matches the IF statement condition it will change the users identity in SharePoint. The new identites are built off the samaccountname from the get-aduser command.</span>
</div>

<span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;">There are a few things that you will need to change in the script. Most noticeably are the domain names, and the $sourceDN comparisons in the IF statements. Also duplicate the IF statements for each domain that you have users in. You will also want to add all service accounts used with SharePoint to the exclusion statement and any others you deem necessary. (You really only need to add the superreader, super writer, and crawl service accounts to the exclusion list, but I though it would be good to add all managed accounts to be safe.</span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;">Before or after running the script (I did this before) change the authentication for the web application to NTLM or kerberos claims, (disabling the ADFS provider).</span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;">The script may take a long time to run depending on the size of your environment so i&#8217;d recommend running this after hours on a weekend. Also in our scenario we were trying to work toward consolidating domains, so we will likely have to do this kind of process for every-time we collapse a user domain into domain1.</span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;">I also felt it prudent to write a reversal script. The resversal script undoes the identity change only, it will basically do the same thing as the ADFS to windows script. It just changes back to an ADFS claim ID (which is WAY easier). Both scripts are linked below.</span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;"><br /> </span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;">SCRIPTS:</span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;"><a href="https://sites.google.com/a/tupkers.net/pkf/home/scripts/SP2010-Migrate-AllUserstoWinClaims%20-%20Generic.ps1?attredirects=0&d=1">Migrate to Windows authentication</a></span>
  
<span style="font-family: 'Segoe UI', Verdana, Arial;"><a href="https://sites.google.com/a/tupkers.net/pkf/home/scripts/SP2010-Migrate-AllUserstoADFSClaims%20-%20Generic.ps1?attredirects=0&d=1">Migrate to ADFS authentication</a></span>

UPDATE: I forgot to mention that if you have SIDhistory set in your stub account domain (domain1 in the example) then the people picker will have all kinds of problems with Windows authentication. (so will Exchange 2010 and granting access to shared mailboxes). I&#8217;d recommend this post. <http://blogs.technet.com/b/rgullick/archive/2010/05/15/sharepoint-people-picker.aspx> It explains the issue with SIDhistory very well. You can get around the SIDHistory issue by not using the people picker and just typing in users in the format domain2\username and then clicking the resolve button. Although I don&#8217;t think thats a realistic option.

Update 2012-4-16: we ran this in in production. It seems to have worked pretty well. I&#8217;ve updated the script though because of some bad programming on my part. Apparently it&#8217;s important to null out your variables if they are going to be reused in a for loop. 🙂 Needless to say there were some issues, but happily not as many as there could have been. I&#8217;ll updating the script to reflect these additions. I think I also need to add some logic to detect if the get-ADuser returns nothing exit the if statement. I&#8217;ll update the script once I get it figured out.

&nbsp;