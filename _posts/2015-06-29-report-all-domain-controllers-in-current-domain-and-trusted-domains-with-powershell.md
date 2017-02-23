---
id: 376
title: Report all domain controllers in current domain and trusted domains with powershell
date: 2015-06-29T13:54:29+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=376
permalink: /2015/06/29/report-all-domain-controllers-in-current-domain-and-trusted-domains-with-powershell/
categories:
  - Active Directory
---
I recently had need to come up with a way to document large chunks of our active directory environment. i came up with the below script. It gets all direct trusts then gets domain controller information for each DC in the trusted domains.

If a domain doesn't have web services enabled or isn&#8217;t reachable, it just records the domain name with empty values.

Right now it's gathering Domain, server name, global catalog status, IPV4 address, IPV6 address, site, OS, and service pack for all reachable DCs.

{% highlight powershell %}
Import-Module ActiveDirectory

$report = @()
$ADdomains = @()
$ADDomainTrusts = @()

#get domain trusts
$ADDomainTrusts = Get-ADObject -Filter {ObjectClass -eq "trustedDomain"}
$ADdomains = $ADDomainTrusts | select -Expand Name
#add the local domain to the list
$ADdomains += (Get-ADDomain).DNSRoot


foreach ($domain in $ADdomains) {
    $DCs = $null
    $DCs = (Get-ADDomain -Server $domain).replicadirectoryservers
    $DCs | ForEach-Object {
        $DC = $null
        $DC = Get-ADDomainController $_ -Server $domain
        $obj = New-Object System.Object
        $obj | Add-Member -Type NoteProperty -Name Domain -Value $domain
        $obj | Add-Member -Type NoteProperty -Name Server -Value $DC.HostName
        $obj | Add-Member -Type NoteProperty -Name IsGlobalCatalog -Value $DC.IsGlobalCatalog
        $obj | Add-Member -Type NoteProperty -Name IPv4Address -Value $DC.IPv4Address
        $obj | Add-Member -Type NoteProperty -Name IPv6Address -Value $DC.IPv6Address
        $obj | Add-Member -Type NoteProperty -Name Site -Value $DC.Site
        $obj | Add-Member -Type NoteProperty -Name OperatingSystem -Value $DC.OperatingSystem
        $obj | Add-Member -Type NoteProperty -Name OperatingSystemServicePack -Value $DC.OperatingSystemServicePack
        $report += $obj
    }
}
$filename = $ENV:Userprofile + "\desktop\" + (Get-ADDomain).DNSRoot + ".csv"

$report | Export-Csv -NoTypeInformation $filename
{% endhighlight %}
