---
title: "Share files between your phone and laptop"
date: 2022-02-10T10:02:24Z
lastmod: 2022-04-29T10:02:24Z
draft: false 
keywords: [ssh termux]
description: ""
tags: [ssh]
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

*Requirements: <br>
Laptop: openssh, a file manager <br>
Phone: Termux or an SSH server app, a file manager capable of connecting to a file server <br>
Prerequisite: Both devices should be connected to the same network* 
<!--more-->
## Getting things ready
Normally, a Linux distro comes with openssh pre-installed. If not, it should be available in your distro’s repository.

Have SSH server run at startup with (run as root)
```bash
systemctl enable sshd.service
systemctl start sshd.service
``` 
If you are one of those people who uses OpenRC, run (as root)

```bash
rc-update add sshd default
rc-service sshd start 
```

If you do not fall in either category, then you probably know what to do.

The config file is `/etc/ssh/sshd_config`.

## On Phone 
Now there are multiple ways to get SSH server on your phone. You can get one on Termux, or install one of the several apps on Play Store or F-Droid.

I personally use [SSH/SFTP Server – Terminal](https://play.google.com/store/apps/details?id=net.xnano.android.sshserver) (contains ads), it is good enough and just werks™. You set up a new username and password, select required directories and start. By default it listens on port 2222 .

**If you are using Termux**, you can install the openssh package and start with `sshd`. By default it listens on port 8022.

The config file is ``$PREFIX/etc/ssh/sshd_config`.

You can have sshd start on boot by configuring Termux:Boot.

## Connecting through SFTP
Once you have got the server up and running, all that remains is you connect through SFTP and start transferring files. On your laptop, use a file manager that supports connecting through SFTP (nautilus, dolphin, etc.). You either type the address in the address bar or in a separate “Network Location” (depends on the file manager). Whatever the case, you enter your IP address with port (`sftp://[YOUR IP ADDRESS]:[PORT]`). It should ask for your username and password (for the machine you are trying to connect). Once you are connected, it should display your files. Now copy files and folders like you normally do.

On your phone, install a file manager that supports SFTP. I use [Cx File Explorer](https://play.google.com/store/apps/details?id=com.cxinventor.file.explorer). Go to **Network > New location > Remote > SFTP**. Enter username, password, address and port of your laptop and you should be good to go.

Alternatively, you can also use command line to transfer files using the tool `sftp`. You connect using `sftp -P [port] [username]@[ip address]`. This method works both on laptop and in Termux.

## Something about SSH
If you are entirely new to SSH, you should look into [setting up a key pair](https://www.cyberciti.biz/faq/how-to-set-up-ssh-keys-on-linux-unix/) for your devices. It is more secure than using your password.
