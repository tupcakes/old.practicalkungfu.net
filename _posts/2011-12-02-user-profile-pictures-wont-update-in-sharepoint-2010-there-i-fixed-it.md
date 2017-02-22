---
id: 59
title: 'User profile pictures won&#8217;t update in SharePoint 2010 (There! I fixed it!)'
date: 2011-12-02T21:51:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=59
permalink: /2011/12/02/user-profile-pictures-wont-update-in-sharepoint-2010-there-i-fixed-it/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2011/12/user-profile-pictures-wont-update-in.html
categories:
  - Automation
  - Script
  - Sharepoint
---
So I was trying to get the user&#8217;s profile pictures to show up on their user profiles in SharePoint 2010. It worked great until I got to the part of running Update-SPProfilePhotoStore.

Update-SPProfilePhotoStore : Error processing the photo URL User Photos/Profile
  
Pictures/0c37852b-34d0-418e-91c6-2ac25af4be5b_10.jpg for user i:05.t|adfs20ser
  
ver|michael.tupker@company.com: System.UriFormatException: Invalid URI: A Do
  
s path must be rooted, for example, &#8216;c:\&#8217;.

Um, why is it telling me that a Dos path must be rooted? I&#8217;m working with a URL here! I after much fighting and swearing at the User Profile Service and it&#8217;s apparently broken powershell command. I decided to write my own version of Update-SPProfilePhotoStore. I don&#8217;t think it&#8217;s as robust as the one that is supposed to work, but at least now all the employees have pictures on their profiles. I still don&#8217;t know why the built in cmdlet doesn&#8217;t work. We are have up to the august 2011 CU installed. We had not tried running the Update-SPProfilePhotoStore cmdlet before this.

There are a few things that you will need to set.

<pre class="brush: powershell; gutter: false">$mySiteUrl
$upAttribute
$newpictureURL
$picturePath</pre>

As well as the searchbase on the get-aduser cmdlet.

<pre class="brush: powershell; gutter: false">#########Begin script
Import-Module ActiveDirectory -ErrorAction SilentlyContinue
Add-PsSnapin Microsoft.SharePoint.PowerShell -ErrorAction SilentlyContinue
Start-SPAssignment -Global
#creates connection to UPS
$mySiteUrl = "https://mysite.corp.net"
$upAttribute = "PictureURL"
$site = Get-SPSite $mySiteUrl
$context = Get-SPServiceContext $site
$profileManager = New-Object Microsoft.Office.Server.UserProfiles.UserProfileManager($context)
$profiles = $profileManager.GetEnumerator()
$newpictureURL = "https://mysite.corp.net/ADPictures/"
$picturePath = "E:\Scripts\SyncPics\ProfilePictures"
#gets all pictures from AD and save them locally
$users = Get-ADUser -Filter * -SearchBase "OU=Accounts,DC=company,DC=net" -Properties mail,thumbnailphoto
$users | foreach-object {$username = $_.mail; $_.thumbnailphoto | Set-Content $picturePath\$username.jpg -Encoding byte}
$spWeb = Get-SPWeb -Identity $mySiteUrl
$spFolder = $spWeb.GetFolder("ADPictures")
$spFileCollection = $spFolder.Files
#loops through all profiles, uploads the picture to the library and sets the profile picture
Foreach ($profile in $profiles) {
    $email = $profile["workemail"].value
    Write-Host $email
    $file = Get-ChildItem $picturePath -filter "$email.jpg"
    #check if file exists. if true upload pic to library and set picture URL
    If ({Test-Path $picturePath\$email.jpg} -and $file.length -gt 1) {
        $filename = $file.name
        $spFileCollection.Add("ADPictures/$($filename)",$file.OpenRead(),$true)
        $profile[$upAttribute].Value = $newpictureURL + $filename
        $profile.Commit()
    }
    else {
        $profile[$upAttribute].Value = $Null
        $profile.Commit()
    }
}
Stop-SPAssignment -Global</pre>

Update 2012-07-16: So I finally got my logon and resource domain connections setup and working last week ([see this blog post](http://practicalkungfu.net/2012/07/13/sharepoint-2010-setting-up-the-upa-to-use-a-logon-and-resource-forest-for-sync/ "see this blog post")). Today I tried the Update-SPProfilePhotoStore cmdlet again now that we are off of ADFS and and using good old windows auth, guess what it worked.

<pre class="brush: powershell; gutter: false">Update-SPProfilePhotoStore -MySiteHostLocation https://devserver.domain.local/my -CreateThumbnailsForImportedPhotos $true</pre>

I ran the above command as it appears to be actually creating pictures in the User Photos picture library in the my site host.

<a href="http://practicalkungfu.net/2011/12/02/user-profile-pictures-wont-update-in-sharepoint-2010-there-i-fixed-it/userphotoslibrary/" rel="attachment wp-att-150"><img class="alignnone size-full wp-image-150" title="userphotoslibrary" src="http://practicalkungfu.net/wp-content/uploads/2011/12/userphotoslibrary.png" alt="" width="992" height="24" srcset="http://practicalkungfu.net/wp-content/uploads/2011/12/userphotoslibrary.png 992w, http://practicalkungfu.net/wp-content/uploads/2011/12/userphotoslibrary-300x7.png 300w, http://practicalkungfu.net/wp-content/uploads/2011/12/userphotoslibrary-500x12.png 500w" sizes="(max-width: 992px) 100vw, 992px" /></a>

I&#8217;m still not sure why the cmdlet fails when sharepoint is using an external identity provider like ADFS, (I&#8217;ve also seen reports that this happens with FBA claims as well). It may still be a product of the CU we are on (aug 2011) and may be fixed in a later CU. I will probably never end up finding out for sure. 🙁

Just an FYI, if you have a large user base expect this to create a LOT of pictures. I believe it creates 3 different pictures sizes for each user. It shouldn&#8217;t be a problem because of sharepoint 2010&#8217;s list and library limits (unless you have over 10 million employees). 🙂