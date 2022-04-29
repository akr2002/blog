---
title: "Copy Sparse Files"
date: 2021-08-05T07:12:29Z
lastmod: 2022-04-29T07:12:29Z
draft: false 
keywords: [rsync sparse]
description: ""
tags: [rsync]
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

Using normal methods to copy sparse files will only take up so much time and actual storage. Pass `-S` to rsync to tell you are transferring sparse files and it take care of it.
<!--more-->
For other methods to copy a sparse file, refer to this [StackExchange post](https://serverfault.com/questions/665335/what-is-fastest-way-to-copy-a-sparse-file-what-method-results-in-the-smallest-f).
