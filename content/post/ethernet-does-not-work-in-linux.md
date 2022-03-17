---
title: "Ethernet Does Not Work in Linux"
date: 2022-03-17T23:59:14+05:30
lastmod: 2022-03-17T23:59:14+05:30
draft: false
keywords: [ethernet, dhcpcd, networkmanager]
description: ""
tags: [ethernet]
categories: [linux]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true 
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

<!--more-->
## The problem
There is a bit of a story here. I had both `dhcpcd` and `NetworkManager` running simultaneously. After wasting a good chunk of my time I found that you are not supposed to run both at a given time. 

## The solution
Disable either `dhcpcd` or `NetworkManager` and it should be working again after a reboot.
