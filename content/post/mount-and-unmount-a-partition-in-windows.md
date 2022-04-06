---
title: "Mount and Unmount a Partition in Windows"
date: 2020-10-24T18:50:31+05:30
lastmod: 2022-04-06T18:50:31+05:30
draft: false
keywords: [mount diskpart mountvol partition]
description: ""
tags: []
categories: [windows]
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

Open elevated Command Prompt by hitting **Win+R > cmd > Ctrl+Shift+Enter > Yes**.

## Using DiskPart
Type in `diskpart` and hit Enter.

Next, enter `list vol`. It will list all drives and partitions.

Note the volume number of the desired partition and enter `sel vol <volume number>`.

### Mount
Assign it a drive letter currently not in use. It can be done by entering `assign letter = <letter>`. If you see **DiskPart assigned the drive letter or mount point**, then the partition mounted successfully.

{{% center %}}
{{% figure src="/img/diskpart.png" title="This is what it looks like on my laptop" alt="Screenshot of diskpart with the above mentioned operations" %}}
{{% /center %}}

Leave diskpart by typing `exit`. Now you can use it as normal from File Explorer. Enter `start <letter>:` to open the partition in File Explorer. Do not forget the colon (:).

### Unmount
Enter `remove letter = <letter>` in diskpart. If you get **DiskPart successfully removed the drive letter or mount point**, then it unmounted successfully.

## Using mountvol
### Mount
Enter `mountvol`. It will list all connected volumes with their UUID. Copy the UUID and mount it to a mount point by entering `mounvol <mount point> <UUID>`. Now you should be able to open it as normal from File Explorer. To do it, enter `start <mount point>`.

{{% center %}}
{{% figure src="/img/mountvol.png" title="On my laptop it looks like this" alt="Screenshot of mountvol with the above mentioned operations" %}}
{{% /center %}}

### Unmount
Enter `mountvol <mount point> /P`. It should unmount the partition.

## Next Step
Take care.
