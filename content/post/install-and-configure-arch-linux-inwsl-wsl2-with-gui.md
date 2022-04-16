---
title: "Install and Configure Arch Linux in WSL/WSL2 with GUI"
date: 2022-04-16T13:57:56+05:30
lastmod: 2020-10-25T13:57:56+05:30
draft: false
keywords: [wsl arch]
description: ""
tags: [wsl]
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

<!--more-->

This article is divided into two parts: (i) Install Arch in WSL, (ii) Configure as per your needs. Each part can be followed independently so if you are not a fan of GUI programs in WSL, leaving the part is perfectly fine.

## Part 0: FOr those running Windows 10 2004 or above
Download and install Linux kernel updae package from [Microsoft's site](https://docs.microsoft.com/en-us/windows/wsl/wsl2-kernel). Set default version to WSL2 by running
```powershell
wsl --set-default-version 2
```

## Part I: Installation
### Automated installation
Assuming you have [Scoop](https://scoop.sh/) installed, this part reduces to
```powershell
scoop install archwsl
```

### Manual installation
Head to [https://github.com/yuk7/ArchWSL/releases](https://github.com/yuk7/ArchWSL/releases) and download the `.appx` and `.cer` files.

Now double click the `.cer` file and follow the prompts to install the certificates neecssary for the installation of Arch WSL. When done, double-click the `.appx` file to install it.

Once there, open Arch from Start menu and let it install. When done, you should see an installation complete message. If you encountered problems with the installation, see the issues at [https://github.com/yuk7/ArchWSL/issues](https://github.com/yuk7/ArchWSL/issues). If there is none, feel free to open an issue.

{{% center %}}
{{% figure src="/img/arch-wsl-gui1.png" alt="Screenshot of Arch WSL window with message Installation Complete" title="Oh, it's completed..." %}}
{{% /center %}}

## Part II: Configuration
Open Arch again. It should be logged in as root.

Uncomment the servers closest to your region in `/etc/pacman.d/mirrorlist`. Now run `pacman -Syyu` to fetch and install updates.

If you get `invalid or corrupted package (PGP signature)` error, do not delete the downloaded packages. Run the following commands one by one as root
```bash
rm -R /etc/pacman.d/gnupg
rm -R /root/.gnupg/
gpg --refresh-keywordspacman-hey --init && pacman-key --populate archlinux
pacman -S archlinux-keyring
pacman -Syyu
```
It should install fine.

### Create a normal user account
Running everything as root on a normal machine is a pretty stupid idea. Create a normal user:
```bash
useradd -m -g users -G wheel,storge,power -s /bin/bash <username>
```
It will add a user with whatever you used in `<username>`. Set a password for this user with
```bash
passwd <username>
```
Uncomment the following line after running `visudo`:
```
%wheel ALL=(ALL) ALL
```

### Time to install a Desktop Environment
First, we install `xorg`, `xorg-server` and then `xfce4`. You may install a desktop environment of your choice.
```bash
pacman -S xorg xorg-server
pacman -S xfce4
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0
export LIBGL_ALWAYS_INDIRECT=1
```

While the DE is being installed, grab [VcXsrv](https://sourceforge.net/projects/vcxsrv/) or (again) you should be fine with your favourite X Server. Alternativaly it can be installed via scoop with
```powershell
scoop install vcxsrv
```

Hit *Win+Q*, type *XLaunch* and hit Enter. Once there, select *One Large Window* (just for the sake of it, if you wish to see the DE in action). Check *Disable access control*, leave the rest as is and click *Next* ad then *Finish*. Allow access if prompted by firewall.

{{% center %}}
{{% figure src="/img/vcxsrv-settings.png" alt="Screenshot of VcXsrv extra settings. All checkboxes are ticked" title="VcXsrv extra settings" %}}
{{% /center %}}

## Result
{{% center %}}
{{% figure src="/img/arch-wsl-gui1.png" alt="Multiple floating Linux programs on Windows dwm" title="Linux programs look native to Windows" %}}
{{% /center %}}

{{% center %}}
{{% figure src="/img/arch-wsl-gui2.png" alt="Multiple floating Linux programs on XFCE4 desktop environment" title="These programs maintain Windows title bar on the desktop environment" %}}
{{% /center %}}
