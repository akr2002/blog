---
title: "Set up an NFS Server on Devuan"
date: 2022-02-19T10:17:07Z
lastmod: 2022-04-29T10:17:07Z
draft: false 
keywords: [nfs devuan]
description: ""
tags: [nfs] 
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
Pretty straightforward. 
<!--more-->
Install the necessary packages
```bash
apt-get --no-install-recommends install nfs-kernel-server
```
Create virtual root
```bash 
mkdir /nfs # can be /srv, /export or whatever
```
Create a directory (or more) under virtual root directory
```bash
mkdir /nfs/home 
```
Mount filesystems to be exported under virtual root directory
```bash
mount --bind /home /nfs/home 
```
Make the mount points persistent. Append them to `/etc/fstab`.
```bash
/home /nfs/home none bind 0 0 
```
Put the following in `/etc/exports` (assuming access is granted to the clients in the `192.0.2.0/24` IP network. Client access can also be specified as a single host using IP address or fully qualified domain name, or * character to grant access to all clients).
```
/nfs 192.0.2.0/24(insecure,rw,sync,no_subtree_check,crossmnt,fsid=0)
/nfs/home 192.0.2.0/24(insecure,rw,sync,no_subtree_check)
```
Configure the daemon. Edit `/etc/conf.d/nfs`
```
OPTS_RPC_NFSD="8 -N 2 -V 3 -V 4 -V 4.1"
```
Start NFS server (assuming OpenRC. See your init system’s documentation)
```bash
rc-service nfs-kernel-server start 
```
Start NFS server at boot
```bash 
rc-update add nfs-kernel-server default
```
See detailed (and more) instructions on [Gentoo wiki](https://wiki.gentoo.org/wiki/Nfs-utils).
