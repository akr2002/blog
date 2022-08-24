---
title: "Display Progress in nix-env"
date: 2022-08-24T11:02:38+05:30
lastmod: 2022-08-24T11:02:38+05:30
draft: false 
keywords: [nix progress]
description: ""
tags: [nix]
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
There is a wall of text but how much stuff has happened?
<!--more-->
A lot of stuff scrolls past telling X packages have been built but gives no idea about the progress. Pass `--log-format bar-wth-logs` to `nix-env`.
```
nix-env --log-format bar-with-logs -iA nixpkgs.<package>
```

It will put a nice status in the bottom telling about how much packages have been downloaded and built and how much is left.

I tried putting the option in the config file but it did not work. I could not find the relevant documentation for it either.
