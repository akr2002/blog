---
title: "Install GTK2 Libraries on Gentoo"
date: 2022-05-04T03:18:39Z
lastmod: 2022-05-04T03:18:39Z
draft: false 
keywords: [gtk2 gentoo]
description: ""
tags: [gentoo]
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
Emerge `x11-themes/gtk-engines`
<!--more-->
While the solution may seem trivial, I spent hours trying to figure it out since emerging `x11-libs/gtk+` will only be built with GTK3 support. Emerging the above package will rebuild it with GTK2 support, which the [wiki](https://wiki.gentoo.org/wiki/GTK#GTK-2_and_GTK-3) fails to state.
