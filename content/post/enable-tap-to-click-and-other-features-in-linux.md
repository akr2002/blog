---
title: "Enable Tap to Click and Other Features in Linux"
date: 2021-03-03T06:46:01Z
lastmod: 2022-04-29T06:46:01Z
draft: false 
keywords: [linux synaptics touchpad driver]
description: ""
tags: [touchpad]
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

It should be easy.

<!--more-->

## If your touchpad is not working
Edit `/etc/default/grub` to pass kernel parameter ([docs](https://www.kernel.org/doc/html/v4.10/admin-guide/kernel-parameters.html)):
```
GRUB_CMDLINE_LINUX_DEFAULT="i8042.nopnp=1"
```

Leave other paramters as is. Generate configuration file as normal and reboot.

## Make touchpad more useful

Works for most Synaptics and ALPS touchpads. Requires `xf86-input-synaptics`.

Edit `/etc/X11/xorg.conf.d/70-synaptics.conf`
```
Section "InputClass"
    Identifier "touchpad"
    Driver "synaptics"
    MatchIsTouchpad "on"
        Option "TapButton1" "1"
        Option "TapButton2" "3"
        Option "TapButton3" "2"
        Option "VertTwoFingerScroll" "on"
        Option "HorizTwoFingerScroll" "on"
EndSection
```

Log out and log in.

## More info and parameters 
[Touchpad Synaptics - ArchWiki](https://wiki.archlinux.org/index.php/Touchpad_Synaptics)

[libinput - ArchWiki](https://wiki.archlinux.org/index.php/Libinput)
