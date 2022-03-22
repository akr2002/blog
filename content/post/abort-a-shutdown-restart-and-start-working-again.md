---
title: "Abort a Shutdown Restart and Start Working Again"
date: 2020-04-05T18:54:46+05:30
lastmod: 2022-03-22T19:10:46+05:30
draft: false
keywords: [shutdown, windows]
description: ""
tags: [shutdown, windows]
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

I don't know why would someone want to abort a shutdown or a restart and start working again.

**Prerequisite:** The computer must be in Shutting Down/Restarting screen and be crazy fast before the shutdown/restart process completes.

## Time to get our hands dirty
Shutdown/Restart as usual and press `Ctrl+Alt+Del`. Do nothing and click on `Cancel` button.

You'll be presented with a black screen. Now press `Ctrl+Shift+Esc` to open Task Manager.

Click `File > Run new task`.

Type in `explorer.exe` and hit Enter.

The desktop should appear now. Everything works fine except Start menu and UWP apps do not work.

To open apps, press `Win+E`. In address bar, type in `Shell:AppsFolder` and hit Enter.

You will be presented with a list of installed apps and programs. Double-click a program and start working.

To get Start menu and UWP apps working, reboot as normal.

That's it.
