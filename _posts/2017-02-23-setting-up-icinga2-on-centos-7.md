---
layout: post
title:  "Setting up Icinga2 on Centos 7"
date:	2017-02-23 11:45:00 +0000
author: Mike Tupker
---

Icinga2 is a very robust, almost to much, monitoring system. After trying a few others LibreNMS, Zabbix, and a few others, I decided that icinga2 was the best one to work with for my purposes.

This will get a very simple one server setup going. Icinga2 farms can get a LOT bigger though. Luckily they scale pretty well.

For the mail command which is used in notifications.
{% highlight bash %}
yum install -y mailx
{% endhighlight %}

Install the Icinga2 repo
{% highlight bash %}
yum -y install https://packages.icinga.org/epel/7/release/noarch/icinga-rpm-release-7-1.el7.centos.noarch.rpm
{% endhighlight %}

Install Icinga2
{% highlight bash %}
yum -y install icinga2
systemctl enable icinga2
systemctl start icinga2
{% endhighlight %}

You should now be able to see the installed features.
{% highlight bash %}
icinga2 feature list
{% endhighlight %}

Enable external commands.
{% highlight bash %}
icinga2 feature enable command
{% endhighlight %}

Install the monitoring plugins and restart icinga2.
{% highlight bash %}
yum -y install nagios-plugins-all
systemctl restart icinga2
{% endhighlight %}


The above steps get Icinga2 installed but it doesn't have a web interface, so next we'll install MariaDB and Icinga2web. Icinga2web uses MariaDB to store it's configuration.
Setting up the DB. For the last step, you are just locking down the mariadb instance a bit for better security.
{% highlight bash %}
yum -y install mariadb-server mariadb
systemctl enable mariadb
systemctl start mariadb
mysql_secure_installation
{% endhighlight %}

{% highlight bash %}
yum -y install icinga2-ido-mysql
{% endhighlight %}

Next we created the DB so open a SQL console.
{% highlight bash %}
mysql -u root -p
{% endhighlight %}

You will want to change the password for the icinga user in the GRANT command below to something safe.
{% highlight SQL %}
CREATE DATABASE icinga;
GRANT SELECT, INSERT, UPDATE, DELETE, DROP, CREATE VIEW, INDEX, EXECUTE ON icinga.* TO 'icinga'@'localhost' IDENTIFIED BY 'icinga';
{% endhighlight %}

Import the DB schema
{% highlight bash %}
mysql -u root -p icinga < /usr/share/icinga2-ido-mysql/schema/mysql.sql
{% endhighlight %}

Enable the IDO Mysql module.
{% highlight bash %}
icinga2 feature enable ido-mysql
{% endhighlight %}

Install and setup the web server. Apache in this case.
{% highlight bash %}
yum -y install httpd
systemctl enable httpd
systemctl start httpd
{% endhighlight %}

Now we can install icingaweb2. You can omit php-ldap if you aren't going to use ldap authentication.
{% highlight bash %}
rpm --import http://packages.icinga.org/icinga.key
curl -o /etc/yum.repos.d/ICINGA-release.repo http://packages.icinga.org/epel/ICINGA-release.repo
yum makecache

yum -y install epel-release

yum -y install icingaweb2 icingacli
yum -y install php-ldap php-mysql
{% endhighlight %}

Set the php timezone using your favorite text editor. /etc/php.ini
{% highlight bash %}
date.timezone = America/Chicago
{% endhighlight %}

When running the setup icingaweb2 will do a http check that will fail unless an index.html exists. Then just restart apache.
{% highlight bash %}
touch /var/www/html/index.html
systemctl restart httpd
{% endhighlight %}

You will still need to generate a token for the web setup though. To do so run:
{% highlight bash %}
icingacli setup token create
{% endhighlight %}
The token will be needed for the web setup.

You should now be able to go to: http://yourIP/icingaweb2/setup


At this point you should have a working icinga2 and icingaweb2 setup. You can either start adding hosts and services via text files, or as I like to do, install Director. Director is wonderful and makes the whole icinga2 experience so much better. I'll talk about it in the next post.
