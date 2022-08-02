---
title: "Stream Audio From YouTube Video with mpv"
date: 2022-08-02T17:48:21+05:30
lastmod: 2022-08-02T17:48:21+05:30
draft: false 
keywords: [stream audio youtube mpv]
description: ""
tags: [mpv]
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
Save resources on your computer with this one trick!
<!--more-->
## What you will need
- mpv for playing audio 
- youtube-dl or one of its many forks for streaming YouTube videos

## How to do it
Tell mpv you do not want video playback
```bash
mpv --no-video $url 
```
where `$url` is the link to the YouTube video.
