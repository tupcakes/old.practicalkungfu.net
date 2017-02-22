---
id: 64
title: '>Updated: Sharepoint 2010 User Profile Service &#8211; Changing Properties with Powershell'
date: 2012-04-02T16:57:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=64
permalink: /2012/04/02/updated-sharepoint-2010-user-profile-service-changing-properties-with-powershell/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2012/04/updated-sharepoint-2010-user-profile.html
categories:
  - Script
  - Sharepoint
---
>I&#8217;ve decided to add an updated post regarding working with the UPS using powershell since I&#8217;ve learned a few things since then.

### Connecting to the Profile Manager

<div>
  I&#8217;ve seen a couple ways to connect to the profile manager, but the most elegant so far has been: http://blogs.technet.com/b/heyscriptingguy/archive/2011/11/12/use-powershell-to-edit-sharepoint-profiles.aspx
</div>

<div>
</div>

<div>
  The code from the URL above is shown below. I really like what this person came up with since it uses the get-spsite cmdlet and works universally on any farm.
</div>

<div>
</div>

<div>
  $Site = Get-SPSite -Limit 1    #Get Site for Service context<br /> $ServiceContext = Get-SPServiceContext($Site)<br /> $ProfileManager = new-object Microsoft.Office.Server.UserProfiles.UserProfileManager($ServiceContext)<br /> $Profiles = $ProfileManager.GetEnumerator()    #Load all profiles into array
</div>

<div>
</div>

<div>
  Everything following this builds off this code segment.
</div>

### Listing Properties

<div>
  This will actually just return the properties for the current user, but thats fine since we just want a list of property names to work with.
</div>

<div>
</div>

<div>
  $profiles.current.Properties | select Name,DisplayName
</div>

<div>
</div>

<div>
  For the sake of space I won&#8217;t show the results, but it will include things like WorkEmail, PictureURL, and PersonalSpace to name a few examples.
</div>

### Updating Properties

<div>
  First off i&#8217;d only recommend doing this if there is a GOOD reason. If something goes south with the code you use (like updating the wrong property), bad things can happen to your user profiles. So always preform things like this on a test system. (and do backups). Also some properties should not be updated outside of the internal UPS processes. Just use common sense when updating profile properties.
</div>

<div>
</div>

<div>
  Foreach ($profile in $profiles) {
</div>

<div>
       $profile[&#8220;WorkEmail&#8221;].Value = &#8220;somenewvalue&#8221;
</div>

<div>
       $profile.Commit()
</div>

<div>
  }
</div>

<div>
</div>

<div>
  The above example, if run, would actually set every users work email to the value on the right side of the = sign. This isn&#8217;t something you would probably want to do but it does show how easily you can update values. For example, with a little conditional logic, you could update Office or the PictureURL.
</div>

<div>
</div>

### Putting it together in a full script

<div>
  Since the Listing Properties section is just for figuring out what properties exist, it wouldn&#8217;t be used in a production script.
</div>

<div>
</div>

<div>
  <div>
    $Site = Get-SPSite -Limit 1    #Get Site for Service context
  </div>
  
  <div>
    $ServiceContext = Get-SPServiceContext($Site)
  </div>
  
  <div>
    $ProfileManager = new-object Microsoft.Office.Server.UserProfiles.UserProfileManager($ServiceContext)
  </div>
  
  <div>
    $Profiles = $ProfileManager.GetEnumerator()    #Load all profiles into array
  </div>
</div>

<div>
</div>

<div>
  <div>
    Foreach ($profile in $profiles) {
  </div>
  
  <div>
         $profile[&#8220;PictureURL&#8221;].Value = &#8220;somenewvalue&#8221;
  </div>
  
  <div>
         $profile.Commit()
  </div>
  
  <div>
    }
  </div>
</div>

<div>
</div>

### Updating a single user

<div>
  The above is good for mass updates but there is always the possibility you would want to update a single user. If would just require a small modification to the foreach statement. I&#8217;d recommend running the code without any assignments or commits to make sure it returns the correct person.
</div>

<div>
</div>

<div>
  <div>
    Foreach ($profile in $profiles) {
  </div>
  
  <div>
    If ($profile[&#8220;accountname&#8221;].value -eq &#8220;domain\username&#8221;) {
  </div>
  
  <div>
    $profile[&#8220;pictureURL&#8221;].value = &#8220;http:\\somesite\somepic.jpg
  </div>
  
  <div>
                    $profile.Commit()
  </div>
  
  <div>
    }
  </div>
  
  <div>
    }
  </div>
</div>