---
title: "Display Package Icons Installed by Nix in Launcher"
date: 2022-08-24T10:20:51+05:30
lastmod: 2022-08-24T10:20:51+05:30
draft: false 
keywords: [nix icons]
description: ""
tags: [nix]
categories: [linux]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: false
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
Of course the icon is displayed only if the package ships a `.desktop` file.
<!--more-->


I have tested this on KDE Plasma. It should work on other desktop environments too. It is not implementation specific.

Run the following manually or put it in your `~/.bashrc`
```bash
export XDG_DATA_DIRS=~/.local/share/:~/.nix-profile/share:/usr/share
ln -s ~/.nix-profile/share/applications/*.desktop ~/.local/share/applications/
```

Now log out and log back in. Or if you are on KDE Plasma, you can run `kbuildsycoca4` to rebuild the application launcher.
