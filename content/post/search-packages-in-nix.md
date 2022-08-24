---
title: "Search Packages in Nix"
date: 2022-08-24T10:53:22+05:30
lastmod: 2022-08-24T10:53:22+05:30
draft: false 
keywords: [nix search]
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
Normally if you run `nix search <package>` it will complain about something experimental.
<!--more-->
Assuming you read the message and understand what you are getting yourself into, follow the instructions to enable search. Alternatively, you can search on [https://search.nixos.org/](https://search.nixos.org/).


Put the following in `~/.config/nix/nix.conf`
```
experimental-features = nix-command flakes
```

Now search will work but it will rebuild cache every time you search. To avoid building cache, run `nix registry pin nixpkgs`.


Search packages with `nix search nixpkgs <package>`.
