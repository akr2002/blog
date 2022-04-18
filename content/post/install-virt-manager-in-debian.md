---
title: "Install Virt Manager in Debian"
date: 2022-04-18T13:39:50Z
lastmod: 2022-04-18T13:39:50Z
draft: false
keywords: [virt-manager kvm libvirt qemu]
description: ""
tags: [libvirt]
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

<!--more-->

`virt-manager` or *Virtual Machine Manager* is a frontend for managing virtual machines through *libvirt*. It primarily targets KVM virtual machines but is also capable of managing Xen and LXC. In this post we will focus on KVM.

*All commands in this post are supposed to be run as root.*

## Installing necessary tools
We will install libvirt, QEMU and a spice client.

```bash
apt-get --no-install-recommends install qemu-utils qemu-kvm virt-manager libvirt-clients libvirt-daemon-system virtinst bridge-utils gir1.2-spiceclientglib-2.0 gir1.2-spiceclientgtk-3.0 virt-viewer libosinfo-bin
```

## Setting up libvirt
### Set up networking
libvirt ships with a default network profile. Here we enable it have it start automatically. This will allow the virtual machines to connect to the internet.

```bash
virsh net-start default
virsh net-autostart default
```

### Add yourself to necessary groups
Here you will add yourself to the necessary groups to avoid running as root every time you want to run a virtual machine. Replace `<user>` with normal user.

```bash
adduser <user> libvirt
adduser <user> libvirt-qemu
adduser <user> kvm
```

### Enable and start libvirt
Apt automatically enables and starts `libvirtd.service`. We do it again anyways.

```bash
systemctl enable libvirtd.service
systemctl restart libvirtd.service
```

Now you should be able to install and manage virtual machines through virt-manager.
