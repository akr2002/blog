---
title: "Change DPI in Linux using command line"
date: 2021-11-10T09:48:47Z
lastmod: 2022-04-29T09:48:47Z
draft: false 
keywords: [dpi linux]
description: ""
tags: [dpi]
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

Using a DE is pretty straightforward but you might be out of luck if you use something like dwm.
<!--more-->
Find correct DPI of your display currently used by X server
```bash
xdpyinfo | grep -B2 resolution
```

This si probably the correct value. If not, you can calculate it by converting your screen size to inches and dividing the resolution by display length.
```bash
xrandr | grep -w connected
```

{{% center %}}
{{% figure src="/img/xrandr.png" title="The output on my machine" alt="Output of the last command" %}}
{{% /center %}}

The above block spits your screen resolution and physical size. Now divide it by display length in inches.

Create/modify the file `~/.Xresources` and append the following line (replace 96 with your DPI)
```
Xft.dpi: 96
```
and have it processed by the startup file (like .xinitrc)
```
xrdb -merge ~/.Xresources
```
