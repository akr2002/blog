---
title: "Trying to Boot Lands You to GRUB Rescue"
date: 2020-09-09T19:35:03+05:30
lastmod: 2022-03-22T19:35:03+05:30
draft: false 
keywords: [grub]
description: ""
tags: [grub]
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

In my case it was Windows 10 + Manjaro dual boot but it should work for other distributions with single boot as well.

<!--more-->

*P.S.: There may be other ways to fix this but I am writing here what worked for me. Also, I am not responsible if your stuff breaks by following the steps here.*

*tl;dr: Boot using a live media, `chroot` to your existing installation and install GRUB to your boot partition.*

## Long version
If the above mentioned method is of least help, here is Manjaro specific procedure.

It usually happens when the partition table gets altered by Windows, either by a Windows update or by manually modifying partitions.

*&lt;rant&gt;Please note that I would not have written this post if all of the existing Manjaro forums had not broken, returning a 404. Also, it would have saved a lot of my time.&lt;/rant&gt;*

Get yourself a live media, boot into your computer and open a terminal. Once there, go figure out your boot partiton and Linux install with `sudo fdisk -l`.

Next, mount your Linux install with `sudo mount /dev/sdX# /mnt`. Replace `/dev/sdX#` with whatever you have in your case.

*Note: At this point, some sites ask you to install `mhwd-chroot` and let it do its sweet job of `chroot`-ing and stuff. Well, I couldn't find it in the official repository and the AUR so we follow a different procedure here.*

`chroot` into your existing Linux install. In my case, it was Manjaro and the live media provides a sweet program called `manjaro-chroot` so all I had to do was enter `manjaro-chroot /mnt`. Refer to [Gentoo wiki](https://wiki.gentoo.org/wiki/Handbook:AMD64/Full/Installation#Mounting_the_necessary_filesystems) or your distribution's documentation for `chroot`-ing into your existing install.

Once there, mount your boot partition. It should be `mount /dev/sdX# /boot/efi`. Again, replace `/dev/sdX#` with whatever you have as boot partition.

Now enter `grub-install /boot/efi`. It should finish without error. Run `grub-mkconfig -o /boot/grub/grub.cfg`.

Exit and reboot. You should now be presented with grub menu.
