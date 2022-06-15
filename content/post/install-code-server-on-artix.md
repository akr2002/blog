---
title: "Install Code Server on Artix"
date: 2022-06-15T06:59:27Z
lastmod: 2022-06-15T06:59:27Z
draft: false 
keywords: [artix, code-server]
description: ""
tags: [code-server]
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
Same as installing code-server on Arch, except you need an OpenRC script.
<!--more-->

## Install using yay
`yay` is a convenient AUR helper. 
```bash
yay -S code-server 
```

## Install using makepkg
```bash 
git clone https://aur.archlinux.org/code-server.git
cd code-server
makepkg -si 
```

## Download and configure OpenRC script
The [script](https://banyan.divineduty.xyz/GNUxeava/code-server-openrc) is pretty generic and should work on most OpenRC-based distributions with minimal tweaks. You just need to download it, make it executable and put your username in line 3.
```bash
cd /etc/init.d/
sudo wget -c https://banyan.divineduty.xyz/GNUxeava/code-server-openrc/raw/branch/master/code-server 
sudo chmod +x code-server 
```

`code-server` reads configuration from `~/.config/code-server/config.yaml`. It is better than sending arguments directly to `code-server` using the script.

## Usage
Start on boot with default runlevel
```bash
sudo rc-update add code-server default 
```

Start the service immediately
```bash
sudo rc-service code-server start 
```
