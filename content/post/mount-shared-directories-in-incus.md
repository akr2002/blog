---
title: "Mount Shared Directories in Incus"
date: 2024-04-27T23:30:08+05:30
lastmod: 2024-04-27T23:30:08+05:30
draft: false
keywords: [incus]
description: ""
tags: [incus]
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

There will arise situations when you will want to share files between host and container. The solution is just a one liner.

<!--more-->

It follows the following syntax:

```
incus config device add <container> <device name> disk source=</path/to/directory/> path=</mount/point/in/conainer>
```

Example:

```
incus config device add conatiner1 movies disk source=/srv/movies path=/mnt/movies
```

Or if you are feeling special, you can directly edit the config with `incus config edit <container>`, following the previous example:

```yaml
dveices:
  movies:
    path: /mnt/movies
    source: /srv/movies
    type: disk
```
