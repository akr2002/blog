---
title: "psql: FATAL: Peer authentication failed for user"
date: 2023-01-04T08:47:34+05:30
lastmod: 2023-01-04T08:47:34+05:30
draft: false
keywords: [postgresql]
description: ""
tags: [database postgres]
categories: [linux]
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
The order of rules in `pg_hba.conf` matters.
<!--more-->

Trying to connect to postgresql database with a standard user fails with `psql: FATAL: Peer authentication failed fr user "user1"`.

The second line of `pg_hba.conf` makes your connection attempt fail:
```
local   all     all     peer
```

The order of rules matter. If the first one that matches the access method, username, database name and source IP range fails, there will be no second attempt. So either remove this line, your place your rule above this one.
