---
title: "Install Deepin Desktop Environment (dde) in Manjaro"
date: 2020-04-19T19:15:12+05:30
lastmod: 2022-03-22T19:15:12+05:30
draft: false
keywords: [linux, deepin]
description: ""
tags: [deepin]
categories: [linux]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: true
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

You're probably here because you took fancy to the beauty of dde and want to try it.

Installing dde on Manjaro shouldn't really be much of an issue as long as Arch supports it.

<!--more-->

Now open a terminal and make sure you have the latest and the greatest of Manjaro:
```bash
sudo pacman -Syu
```

Next, we install `deepin` group and optionally `deepin-extra` if you want.
```bash
sudo pacman -S deepin 
```

Or if you want a more complete desktop environment, enter:
```bash
sudo pacman -S deepin deepin-extra 
```

This should get us covered.

Additionally, I found a nasty bug that locks you out of Deepin desktop environment once you have logged into it and locked your computer. Apparently it is an issue related to keyboard layouts. Adding English US to my keyboard layout and using that to log in seemed to fix the issue. To add English US to your layout, go to `Control Center > Keyboard and Language > Keyboard Layout > Add Keyboard Layout > English (US)`.

For more information regarding the installatiom of Deepin desktop environment, consult the [Arch wiki](https://wiki.archlinux.org/index.php/Deepin_Desktop_Environment) or the [Manjaro wiki](https://wiki.archlinux.org/index.php/Deepin_Desktop_Environment).

End result:
{{% center %}}
{{% figure src="/img/manjaro-deepin.png" alt="Screenshot of Deepin desktop environment on Manjaro" %}}
{{% /center %}}
