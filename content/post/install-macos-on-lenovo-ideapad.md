---
title: "Install MacOS on Lenovo Ideapad"
date: 2022-07-20T20:46:40+05:30
lastmod: 2022-07-20T20:46:40+05:30
draft: false 
keywords: [macos, monterey, hackintosh]
description: ""
tags: [hackintosh]
categories: [hackintosh]
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
It's Hackintosh.
<!--more-->
I was a bit curious about how using MacOS feels like. I had a spare disk lying around so I thought might as well give it a shot while I am still in the mood for it. 

How you install it depends on your hardware. On some it can be as easy as distrohopping, wheareas on the others you might end up patching EFI along with other stuff. That being said, nothing guarantees your experience would be a good one. Some hardware may not work, animations might by choppy and the whole thing may be laggy. However, my mind wouldn't be at ease knowing I quit without even trying.

## Obtaining the image
This is a hard part. Most sites will ask you to download the updater, which, well only works on MacOS. I got it from a dodgy looking website, hidden behind ads, ads and more ads, that do not give you the link unless you click on the ads. I think the person who made the patched images does not even get the money because they host the torrents alongside other patches on their own website. I didn't go for torrent because it did not work. You will find a link to the website that hosts the torrents and patches in the next section.

## Preparing to install
If you are on Intel Core series or certain Lenovo laptops, you will need a patch for the EFI. It's [here](https://olarila.com/files/?dir=OPENCORE1). Once you have finished burning the image to the bootable stick, you will need to mount it, delete the existing EFI directory and put the patched one in its place.

## Installing MacOS
Boot the image. Select Disk Utility and find the storage device for MacOS. Format it with APFS. Going along with the defaults should be fine. Now go back and click on the install button. Follow the promts and it should start installing. This will take a long time with a couple of reboots, to the point you might end up feeling it has frozen. Of course, it depends on the hardware. Apple has no obligation to care about the Mac experience on the devices that are not Macs. 

## Problems encountered
Wrong EFI will boot the image, but nothing will work. So make sure to get the right one.

If the image is unable to discover a touchpad, it will think the device does not have a mouse and will prompt you to connect one. The installer will not start without it.

## My experience
It was slow and laggy. While I was not expecting it to be this bad, I am not surprised with the result. The WiFi does not work as expected, for my laptop boasts a Realtek card. The OS did get the screen resolution right. 

In the end, while I did end up using MacOS, I still don't know what using a Mac feels like. But it was fun nonetheless.
