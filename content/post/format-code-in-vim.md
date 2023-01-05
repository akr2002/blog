---
title: "Format Code in Vim"
date: 2023-01-05T10:53:20+05:30
lastmod: 2023-01-05T10:53:20+05:30
draft: false
keywords: [format indent vim]
description: ""
tags: [vim]
categories: [linux windows]
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
Spend less time formatting your code with this easy trick!
<!--more-->
```
gg=G
```


That's it. `gg` Goes to the top of the file. `=` tells vim to fix the indentation. `G` goes to the end of the file.
