---
title: "Do Not Install Recommended Packages with APT"
date: 2022-07-09T16:26:12+05:30
lastmod: 2022-07-09T16:26:12+05:30
draft: false 
keywords: [apt debian ubuntu]
description: ""
tags: [apt]
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
This applies only if you use Debian or Ubuntu or any distribution that uses apt package manager.
<!--more-->
Normally when you run `apt install`, it will automatically install a lot of optional packages, without which your intended package will work perfectly fine since these are *optional*. They just consume more bandwidth and a lot more diskpace. These will only add up as you update your system over time. 

Now, there are two ways to get rid of this behaviour. Pass `--no-install-recommends` to apt everytime you install a package, or put it in `/etc/apt/apt.conf.d/99-no-install-recommend` to avoid typing it every time.

```
APT::Install-Recommends "false";
APT::Install-Suggests "false";
```
