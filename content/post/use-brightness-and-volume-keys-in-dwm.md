---
title: "Use Brightness and Volume Keys in Dwm"
date: 2021-03-03T06:53:22Z
lastmod: 2022-04-29T06:53:22Z
draft: false 
keywords: [dwm brightness volume]
description: ""
tags: [dwm]
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

Requires `pulseaudio pavucontrol alsa-lib alsa-utils acpilight`

Actual requirements may vary depending on your configuration. I am only writing what worked for me.
<!--more-->

Edit `config.h`

```c
#include <X11/XF86keysym.h>
 
/* volume keys*/
static const char *upvol[] = { "/usr/bin/pactl", "set-sink-volume", "0", "+5%", NULL };
static const char *downvol[] = { "/usr/bin/pactl", "set-sink-volume", "0", "-5%", NULL };
static const char *mutevol[] = { "/usr/bin/pactl", "set-sink-mute", "0", "toggle", NULL };
 
/* backlight */
static const char *brightnessup[] = { "sudo", "xbacklight", "-inc"m "5", NULL };
static const char *brightnessdown[] = { "sudo", "xbacklight", "-dec", "5", NULL };
```

Append the following lines in `static Key keys[]` array
```c
static Key keys[] = {
    { 0, XF86XK_AudioLowerVolume, spawn, {.v = downvol} },
    { 0, XF86XK_AudioMute, spawn, {.v = mutevol }},
    { 0, XF86XK_AudioRaiseVolume, spawn, {.v = upvol} },
    { 0, XF86XK_MonBrightnessUp, spawn, {.v = brightnessup} },
    { 0, XF86XK_MonBrightnessDown, spawn, {.v = brightnessdown} },
}; 
```
