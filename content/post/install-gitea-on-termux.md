---
title: "Install Gitea on Termux"
date: 2021-03-28T06:58:17Z
lastmod: 2022-04-29T06:58:17Z
draft: false 
keywords: [gitea termux]
description: ""
tags: [gitea, termux]
categories: [termux]
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

It's possible.

<!--more-->

Install gitea and sqlite
```bash
apt install gitea sqlite 
```

Run gitea by simply executing gitea and navigate to port 3000 on your phone's IP (or on localhost).

Select *Sqlite3* as Database Type, change *Site Title* to whatever you feel like and leave everything as is (or change some stuff if you know what you are doing). Click in *InstallGitea* to apply your config.

Next, you will be presented to the login page. Create an account and start working as normal.

## End result
{{% center %}}
{{% figure src="/img/gitea.jpg" title="You can also use port forwarding" alt="Screenshot of Gitea homepage" %}}
{{% /center %}}
