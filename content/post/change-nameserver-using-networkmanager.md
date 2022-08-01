---
title: "Change Nameserver using NetworkManager"
date: 2022-08-01T17:59:33+05:30
lastmod: 2022-08-01T17:59:33+05:30
draft: false 
keywords: [nameserver, networkmanager, nmcli]
description: ""
tags: [networking]
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
Do not edit `/etc/resolv.conf`. It will likely be replaced by NetworkManager.
<!--more-->
Open a shell and add nameserver for existing connection.

```bash
nmcli con $ssid ipv4.dns "1.1.1.1 1.0.0.1"
```
The SSID can be found with `nmcli con`.

Replace the IP addresses to your desired ones.

Now restart the connection to apply the changes.

```bash
nmcli con down $ssid
nmcli con up $ssid 
```

Verify with `cat /etc/resolv.conf`.
