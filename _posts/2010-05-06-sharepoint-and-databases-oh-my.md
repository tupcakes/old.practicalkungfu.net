---
id: 45
title: '>Sharepoint and databases, oh my!'
date: 2010-05-06T02:00:00+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=45
permalink: /2010/05/06/sharepoint-and-databases-oh-my/
blogger_blog:
  - practicalkungfu.blogspot.com
blogger_author:
  - ""
blogger_permalink:
  - /2010/05/sharepoint-and-databases-oh-my.html
categories:
  - How To
---
>So my recent work in Sharepoint has led me to the conclusion that I can&#8217;t ignore databases anymore. I need to get my learn on.

To that end I decided that the simplest way to teach myself about databases, specifically MSSQL, was to write a simple database application. I decided on something to track exchange 2007 mailstore sizes over time. I also have been learning powershell so I decided to use that to write to MSSQL.

Right now I have a simple flat database that I made in SQL 2005 Express with the following columns:  
Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;Type  
Entry&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; int  
ServerName&nbsp;&nbsp;&nbsp; nvarchar(50)  
DB&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nvarchar(50)  
SizeinGB&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nvarchar(50)  
Users&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nvarchar(50)  
DateStamp&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nvarchar(50)

Like I said simple. I&#8217;ve been using the following script to insert data into the database.

<span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;"></p> 

<blockquote>
  <p>
    <span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">#Create SQL client object and open a connection</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$dbConn = new-object System.Data.SqlClient.SqlConnection &#8220;server=localhost\SQLExpress;database=ExchangeReports;trusted_connection=true;&#8221;</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$dbConn.Open()</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$sqlQuery = $dbConn.CreateCommand()</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;"><br /></span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">#Get data to insert</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">set-location C:\exchangehistory_v2.0</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$file = Import-Csv mailDBdata-20100429-2106.csv</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;"><br /></span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">Write data to DB</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$file </span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">ForEach-Object{</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$sqlQuery.CommandText = &#8220;INSERT INTO Mailstores (ServerName,DB,SizeinGB,Users,DateStamp) VALUES (&#8216;$($_.server)&#8217;,&#8217;$($_.database)&#8217;,&#8217;$($_.sizegb)&#8217;,&#8217;$($_.usercount)&#8217;,&#8217;$($_.date)&#8217;)&#8221;</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">Write-Host</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">Write-Host $sqlQuery.CommandText</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$result = $sqlQuery.ExecuteNonQuery()</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">}</span><br /><span style="font-family: &quot;Trebuchet MS&quot;, sans-serif;">$dbConn.Close()</span>
  </p>
</blockquote>

<p>
  <span style="font-family: inherit;">Essentially it&#8217;s reading data from a CSV file into a variable and then writing the data in the variable into the database table. Very simple.</span>
</p>

<p>
  <span style="font-family: inherit;">The insert statement took a while to figure out. Note, if you run into problems double check the data types you have set for your database columns. 🙂</span>
</p>

<p>
  Anyway, if I actually get this working in production I may post a more complete workup on the full database and application when it&#8217;s done.
</p>

<p>
  Next up for me Sharepoint 2007 Business Data Catalog. Eeek!</span>
</p>