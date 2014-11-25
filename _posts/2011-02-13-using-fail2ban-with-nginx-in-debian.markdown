---
layout: post
status: publish
published: true
comments: true
title: Using fail2ban with nginx in Debian
summary: fail2ban helps you fight spam and bots but comes with an Apache sample. I converted it to handle Nginx information.
date: 2011-02-13 15:03:16 +0100
categories:
- Linux
tags:
- nginx
- spam
- fail2ban
- security
---
Taking a look at the logwatch mails I see a common pattern of attacks, coming from China and trying to find details of my server configuration, which is something I don't like.

![fail2ban logo](/images/fail2ban-logo.jpg)

Looking around I found [fail2ban](http://www.fail2ban.org/) which is a tool that does some regex matches on the server logs (sshd, httpd, authd, ...) and takes proper actions, like banning the offending IP.

I then installed fail2ban in my Debian box:

    apt-get install fail2ban

Then, I took a look at `/etc/fail2ban/jail.conf` and found some entries for Apache but none for [nginx](http://nginx.org/), my server of choice, so I decided to create a `jail.local` to add some nginx stuff (this is recommended in Debian to allow hassle-free upgrades).

![nginx logo](/images/nginx-logo.png)

I started copying the Apache sections of the default fail2ban as the log files in my case use the same format that allows me to use Awstats easily. Then, I modified my log routes to point to the nginx ones and using Apache rules, if they don't work I'll tune them later.

{% highlight cfg %}
[nginx]
enabled = true
port    = http,https
filter  = apache-auth
logpath = /var/log/nginx*/*error.log
maxretry = 6

[nginx-noscript]
enabled = false
port    = http,https
filter  = apache-noscript
logpath = /var/log/nginx*/*error.log
maxretry = 6

[nginx-overflows]
enabled = false
port    = http,https
filter  = apache-overflows
logpath = /var/log/nginx*/*error.log
maxretry = 2
{% endhighlight %}

Although this is ok, the bots I see don't leave a trace in `error.log` but in `access.log` so I took a look at the `filter.d` folder where an interesting `apache-badbots.conf` was present. Then, I found the default fail2ban documentation in `/usr/share/doc/fail2ban` where there's an usage example of the badbots script. I added I to my `jail.local`:

{% highlight cfg %}
[apache-badbots]
enabled  = true
port    = http,http
filter   = apache-badbots
logpath  = /var/log/nginx*/*access.log
bantime  = 172800
maxretry = 1
{% endhighlight %}

Finally, I added this to the top of the file, to send mails to myself when a rule matches and a host is banned.

{% highlight cfg %}
[DEFAULT]
action = %(action_mwl)s
{% endhighlight %}

Finally, restart the service and start receiving mails:

    sudo /etc/init.d/fail2ban restart

I'm sure this will need further adjustments, but it's a beginning in my bot fighting war. I'll make some updates as I find interesting results.
