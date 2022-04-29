---
title: "Open an Unlisted Removable Drive in Windows"
date: 2020-03-23T06:57:45+05:30
lastmod: 2022-03-17T22:58:45+05:30
draft: false
keywords: [diskpart, removable-drive]
description: ""
tags: [diskpart]
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

It might happen that you connected your USB stick but it did not show up in File Explorer. Maybe it is formatted with a filesystem not recognised by Windows or there might be some other reason. 

<!--more-->
In my case it was an NTFS formatted USB stick which in theory should be recognised by Windows but it did not or it refused to assign it a drive letter as was in my case. So before you think your removable drive has broken and start with a data recovery tool, follow the instructions here.

## Open elevated Command Prompt

Once there, enter `diskpart`

The [DiskPart](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/diskpart) commands help you to manage your PC's drives (disks, partitions ,volumes, or virtual hard disks).

Now type `list vol` and hit enter. It wil list the volumes installed in your computer.
{{% center %}}
{{% figure src="/img/diskpart-list-vol-1.png" title="It was initially a bootable USB drive containing Manjaro 17.06 and I was too lazy to change the label before formatting" alt="Screenshot of the command `list vol`" %}}
{{% /center %}}

Luckily, in my vase all filesystems are recognised by Windows.

Next, enter `sel vol #`, where `#` is the volume number of the removable drive, which in my case is `Volume 6`. So mine looks like this:
{{% center %}}
{{% figure src="/img/dispkart-set-vol-2.png" alt="Screenshot of the command `set vol`" %}}
{{% /center %}}

Now, it is time to assign the removable drive a drive letter so that it can show up in File Explorer. To do that, choose any letter that is not in use except `A` and `B`. They are reserved for floppy drives for historical reasons (and alsi to prevent any unwanted potential issue). So enter `assign letter $`, where `$` is the letter. In my case, it looks like:
{{% center %}}
{{% figure src="/img/diskpart-assign-letter.png" alt="Screenshot of the command `assign letter`" %}}
{{% /center %}}

Enter `list vol` again just to be sure or alternatively you can check in File Explorer.
{{% center %}}
{{% figure src="/img/dispkart-set-vol-2.png" alt="Screenshot of the command `list vol` to confirm our actions" %}}
{{% /center %}}

To exit `DISKPART`, type in `exit` and hit enter.

Now you can work normally witht the removable drive. If your issue is still unfixed maybe it is time to ask in forums and get data recovery tool in action.
