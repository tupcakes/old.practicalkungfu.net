---
layout: post
title:  "Install Icinga director"
date:	2017-02-23 11:45:00 +0000
author: Mike Tupker
---

{% highlight bash %}
{% endhighlight %}

yum install -y git

###setup api user
icinga2 api setup
systemctl restart icinga2

###download director
cd /usr/share/icingaweb2/modules
git clone https://github.com/Icinga/icingaweb2-module-director.git director

###Create director database
mysql -u root -p 
CREATE DATABASE director CHARACTER SET 'utf8';
GRANT ALL ON director.* TO director@localhost IDENTIFIED BY 'director';

cd /usr/share/icingaweb2/modules/director/schema
mysql -u director -p director < mysql.sql


nano /etc/icinga2/zones.conf
###
object Zone "director-global" {
  global = true
}
###




###Create a Database resource
#In your web frontend please go to Configuration / Application / Resources
#and create a new database resource pointing to your newly created database. 
#Please make sure that you choose utf8 as an encoding.

#get api user and pass from: /etc/icinga2/conf.d/api-users.conf

#enable module in icingaweb2
#in director create the schema

