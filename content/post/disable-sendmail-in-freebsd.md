---
title: "Disable sendmail in FreeBSD"
date: 2021-12-11T09:57:29Z
lastmod: 2022-04-29T09:57:29Z
draft: false 
keywords: [sendmail freebsd]
description: ""
tags: [sendmail]
categories: [freebsd]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: true 
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

I do not use emails on my FreeBSD machine so I did not bother setting it up. But it runs at startup and apparently prevents other services from starting till it finishes its job (I cannot ssh into this machine till sendmail decides it has had enough and stops).

<!--more-->

Change these lines to `/etc/mail/mailer.conf` to 
```
sendmail   /usr/bin/true
mailq      /usr/bin/true
newaliases /usr/bin/true
hoststat   /usr/bin/true
purgestat  /usr/bin/true 
```
And in `/etc/periodic/periodic.conf` to 
```
# disable sendmail (mailqueue) cleanups:
daily_clean_hoststat_enable="NO"
daily_status_mail_rejects_enable="NO"
daily_status_mailq_enable="NO"
daily_submit_queuerun="NO"

# disable periodic's emailing by logging!
daily_output=/var/log/daily.log
weekly_output=/var/log/weekly.log
monthly_output=/var/log/monthly.log

# put these into the log, instead of trying to mail them
daily_status_security_inline="YES"
weekly_status_security_inline="YES"
monthly_status_security_inline="YES"
```
And in `/etc/rc.conf` to 
```
# disable sendmail
sendmail_enable="NO"
sendmail_submit_enable="NO"
sendmail_msp_queue_enable="NO"
sendmail_outbound_enable="NO"

# don't allow cron to mail
cron_flags="-m ''"
```

Instructions taken from
[https://gist.github.com/igalic/c77ed494e102977c9fd06ce9b053cda0](https://gist.github.com/igalic/c77ed494e102977c9fd06ce9b053cda0)
