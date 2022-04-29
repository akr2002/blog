---
title: "Use sudo in Windows"
date: 2021-02-18T06:21:50Z
lastmod: 2022-04-29T06:21:50Z
draft: false 
keywords: [sudo]
description: ""
tags: [sudo]
categories: [windows]
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

This si not the same `sudo` you use in \*nix systems. It just shares the name.

It is quite tiresome to launch a new instance of the running program with admin privilege.

## Install
```powershell
scoop install gsudo
```

Other installation methods are in the repo: [https://github.com/gerardog/gsudo](https://github.com/gerardog/gsudo)

## Usage
Prepend `sudo` to the command that requires admin provolege. It will launch UAC.

{{% center %}}
{{% figure src="/img/gsudo.png" title="It feels surreal" alt="Screenshot of sudo in action. It cannot geenrate ssh keys as a normal user but prepending sudo gets the job done." %}}
{{% /center %}}
