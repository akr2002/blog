---
title: "Cannot start X Server on Gentoo"
date: 2022-02-10T10:13:14Z
lastmod: 2022-04-29T10:13:14Z
draft: false 
keywords: [gentoo x server]
description: ""
tags: [xorg]
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

Add yourself to the `tty` group.
<!--more-->
```bash
usermod -aG tty [user]
```
Honsetly, you should be looking at [Gentoo wiki](https://wiki.gentoo.org/wiki/Non_root_Xorg).
