---
title: "Install Archlinux From Existing Linux"
date: 2024-03-09T09:32:03+05:30
lastmod: 2024-03-09T09:32:03+05:30
draft: false
keywords: ["archlinux", "nixos"]
description: ""
tags: [archlinux, nix]
categories: [linux]
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
Sometimes situations arise where I have to install Archlinux on a different partition/drive and I cannot switch to live media. Arch provides [`arch-install-scripts`](https://archlinux.org/packages/extra/any/arch-install-scripts/) to make things easier.
<!--more-->
The entire process is pretty straightforward this way and the experience is similar to that of a live media except you do not have to set up networking to get things started.

## Installing nix
We will be using the `nix` package manager to use `arch-install-scripts`. Reason being a) it's dependencies will be isolated from those of the host system, b) it can be installed on most mainstream Linux distributions. [Install nix](https://nixos.org/download#nix-install-linux) if you haven't already before proceeding to the next section. 

## Spawning a shell
Run `nix-shell -p arch-install-scripts` to spawn a shell with the package. Or you can run `nix shell nixpkgs#arch-install-scripts` if you have [flakes](https://nixos.wiki/wiki/Flakes) enabled. It is just a one time thing so we are not installing it and can be garbage collected on a latter date.

## Getting started
Prepare the target drive for installation. Set up partitions and mount the filesystem.


