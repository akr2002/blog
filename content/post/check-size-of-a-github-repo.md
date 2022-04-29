---
title: "Check Size of a Github Repo"
date: 2021-02-18T14:39:57Z
lastmod: 2022-04-28T14:39:57Z
draft: false
keywords: [size github repo repository]
description: ""
tags: [github]
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

Just a shell script.

Requirements: curl, jq, GNU Coreutils

<!--more-->

```bash
#!/bin/sh
 
sizeof()
{
    curl -s https://api.github.com/repos/$1 | jq '.size' | numfmt --to=iec --from-unit=1024
}
 
size=$(sizeof $1)
echo $size
```

## Usage
Pass username/repo as argument to the script.
