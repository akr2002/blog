---
title: "When pacman says \"error: required key missing from keyring\""
date: 2020-10-20T08:02:14+05:30
lastmod: 2022-04-06T08:02:14+05:30
draft: false
keywords: [pacman]
description: ""
tags: [pacman]
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

<!--more-->

```
warning: Public keyring not found; have you run 'pacman-key --init'?
downloading required keys...
error: keyring is not writable
error: required key missing from keyring
error: failed to commit transaction (could not find or read file)
Errors occurred, no packages were upgraded.
```

This is what happened when I ran `pacman -S zsh` on my new WSL2 install.

I wasted a lot of time trying to fix this thing. I'll jump to the conclusion so you don't have to waste your time too. Entering these (as root) worked for me.
```
rm -r /etc/pacman.d/gnupg/
pacman-key --init
pacman-key --populate archlinux
pacman -Sc
pacman -Syyu
```

Read the thread here: https://bbs.archlinux32.org/viewtopic.php?id=522&p=2
