---
id: 85
title: Defeating the wizard (aka renaming DBs with GUIDs created by the SharePoint 2010 configuration wizard)
date: 2012-04-26T20:37:08+00:00
author: Mike Tupker
layout: post
guid: http://practicalkungfu.net/?p=85
permalink: /2012/04/26/defeating-the-wizard-aka-renaming-dbs-with-guids-created-by-the-sharepoint-2010-configuration-wizard/
categories:
  - MS SQL
  - Script
  - Sharepoint
---
I&#8217;m not a DBA nor do I pretend to be one, however anyone who has supported SharePoint for any length of time will end up in the SQL console at some point (usually more than once). It hurts to admit it but I also used the farm wizard from time to time when settings up SharePoint environments. I&#8217;m not going to go into why the GUIDs in the DB names are bad in this article so just trust me that they are. 🙂

The best resource you can use for changing out the DB names for all your service applications is this technet article: <http://technet.microsoft.com/en-us/library/ff851878.aspx>

It shows the various ways that you can change the services apps to point at the new DB names. However one thing it doesn&#8217;t address is how to change the DB names. After much searching and consulting with a DBA  I know I came up with the following process.

<pre class="brush: sql; gutter: true">--rename the DB in SQL
ALTER DATABASE "olddbname"
MODIFY NAME = newdbname
GO

--Change logical data and log file names
ALTER DATABASE newdbname
MODIFY FILE (NAME = &#039;olddbname&#039;, NEWNAME = &#039;newdbname&#039;)
GO
ALTER DATABASE newdbname
MODIFY FILE (NAME = &#039;olddbname_log&#039;, NEWNAME = &#039;newdbname_log&#039;)
GO

--set the DB to offline to change the file locations
ALTER DATABASE newdbname SET OFFLINE
GO

--change physical file names (locations)
ALTER DATABASE newdbname
MODIFY FILE (NAME=&#039;newDBname&#039;, FILENAME=&#039;F:\Program Files\Microsoft SQL Server\MSSQL10_50.MSSQLSERVER\MSSQL\DATA\newdbname.mdf&#039;)
GO
ALTER DATABASE newdbname
MODIFY FILE (NAME=&#039;newdbname_log&#039;, FILENAME=&#039;F:\Program Files\Microsoft SQL Server\MSSQL10_50.MSSQLSERVER\MSSQL\DATA\newdbname_log.ldf&#039;)
GO

--rename the files in the file system, then bring the DB online
--use gui or this
--ALTER DATABASE newdbname SET ONLINE
--GO</pre>

Just do a find and replace for the strings newdbname and olddbname. You will also want to change the file path to the physical file locations.

Now, this script does a few things. The first couple lines will actually rename the database inside SQL.

The following two modify file commands will change the logical name of the MDF and LDF. I don&#8217;t know how else to describe them, but suffice it to say they are like pointers that SQL uses to reference the actual file locations in the fileystem.

Lastly the script offlines the database and changes where SQL thinks the MDF and LDF files are located.

After that you need to change the actual file names in the filesystem to match the files names you put in the MDF and LDF paths.

The only thing I couldn&#8217;t figure out how to script is actually renaming the files themselves. Now that I think about it I could probably do all of it with powershell. (maybe later).

Quick note about the databases. Currently, to my knowedge, there is no way to rename or create without a GUID, the Application Registry database.

I hope this helps.

UPDATE 4/26/2012: I forgot to mention this script will not rename NDF files and file groups. I&#8217;ll be looking for a way to do that. I&#8217;ll update when I find it.

UPDATE 4/26/2012: Figured out how to update NDFs and filegroups. I would recomend doing these changes in stages.

<pre class="brush: sql; gutter: true">------renaming NDFs
---get the ndf name and file name
--set newndfname, oldndfname, newdbname

ALTER DATABASE newDBname
MODIFY FILE (NAME = &#039;oldndfname&#039;, NEWNAME = &#039;newndfname&#039;)
GO
ALTER DATABASE newdbname SET OFFLINE
GO
ALTER DATABASE newdbname
MODIFY FILE (NAME=&#039;newndfname&#039;, FILENAME=&#039;F:\Program Files\Microsoft SQL Server\MSSQL10_50.MSSQLSERVER\MSSQL\DATA\newndfname.ndf&#039;)
GO


------rename a file group
ALTER DATABASE newDBname
MODIFY FILEGROUP "oldndfname"
NAME = newndfname
GO</pre>

&nbsp;