---
title: "Reduce Size of Initramfs"
date: 2023-04-29T12:15:25+05:30
lastmod: 2023-04-29T12:15:25+05:30
draft: false
keywords: [initramfs kernel initrd]
description: ""
tags: [kernel-development]
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
When I was developing a Linux distribution, I had trouble getting it boot on my machine.
<!--more-->
I thought I was building the kernel wrong and was generating initramfs wrong. So I took Debian's initrd from my machine and used it to boot. It took some time but it booted. So certainly I was generating initramfs wrong.

After digging around on the Internet, I found the problem lies in the large size of the initramfs. My version was several hundred megabytes. For whatever reason, the large size renders it unable to serve its purpose. The kernel modules are not stripped. Stripping the image solves the issue.
```bash
cd /path/to/new-kernel
find . -name *.ko -exec strip --strip-unneeded {} +
```

Reference: [How to reduce the size of the initrd when compiling your kernel?](https://unix.stackexchange.com/questions/270390/how-to-reduce-the-size-of-the-initrd-when-compiling-your-kernel)
