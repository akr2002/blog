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

When working with containers, managing file sharing between the host system and container
can often pose a challenge. However, there is an easy-to-use solution that simplifies this
process using just one line of code! This blog post will guide you on how to share files
effortlessly by leveraging Incus's `incus config` command.

<!--more-->

## The One Liner Solution:

If you're looking to share a specific directory containing your container data, here is the
one-liner syntax for adding the file source and path within the container configuration:

```
incus config device add <container> <device name> <device type> source=/<path/to/directory/> path=/<mount/point/in/container>
```

To give you a better understanding, let's look at an example. Suppose we have a container
named `container1` and want to share files located in the `/srv/movies` directory with a mount point of `/mnt/movies` within the container itself:

```
incus config device add container1 movies disk source=/srv/movies path=/mnt/movies
```

## Alternative Method for Advanced Users:

If you're feeling adventurous and want to directly edit the configuration, Incus provides
an alternative method. With this approach, you can leverage YAML syntax within your
container configuration file with `incus config edit <container>`. Here is how it works using our
previous example:

```yaml
dveices:
  movies:
    path: /mnt/movies
    source: /srv/movies
    type: disk
```

Happy hacking!
