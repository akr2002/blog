---
title: "Fix Headset Microphone Issue in Linux"
date: 2022-03-17T23:48:49+05:30
lastmod: 2022-03-17T23:48:49+05:30
draft: false
keywords: [headset, unplugged]
description: ""
tags: [driver]
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

You may have noticed your plugged in headset shows up as "Headset (unplugged)" in Pavucontrol and it does not take audio input. Your computer is probably missing on some kernel modules for audio capture to work properly. Put the following in `/etc/modprobe.d/alsa-base.conf`
```
options snd-hda-intel model=alc255-acer,dell-headset-multi
```

These modules work even when your device is not from the said OEMs. Additionally, there might already exist a module specifically for your machine. In that case, put that in the file and reboot.
