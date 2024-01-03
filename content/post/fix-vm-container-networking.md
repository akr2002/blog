---
title: "Troubleshooting Internet Connectivity Issues in Virtual Machines (VMs) and Docker Containers"
date: 2024-01-03T13:15:16+05:30
lastmod: 2024-01-03T13:15:16+05:30
draft: false
keywords: [libvirt docker internet]
description: ""
tags: [libvirt docker networkmanager]
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

The ability to run virtual machines (VMs) and docker containers is an integral part of today's modern computing landscape. However, issues with internet connectivity inside these environments often cause significant inconvenience for users who depend on this technology for their daily operations or development workflows. In this article, I will discuss about a specific issue I faced while working on NixOS and Archlinux distributions where internet access was not available within libvirt VMs and docker containers. I'll also detail the investigation process that eventually led to identifying and resolving the problem.
<!--more-->

## Problem Statement & Initial Investigation
Before I begin, I must state that I am using libvirt, docker and NetworkManager. Maybe the problem lies somewhere else if you are using something else like wpa\_supplicant or systemd-resolved.

Recently I faced an issue where I could not access the internet within libvirt VMs and docker containers on both NixOS and Archlinux distributions. I had already explored several potential solutions, including checking iptables configurations, but found no blockages of internet traffic. The realization that the same problem occurred in different operating systems suggested that it was more likely an issue with network configuration rather than software faults.

Initial Steps Taken:
1. Examine `ip a` and `ip r` outputs for further information regarding network interfaces, IP addresses, and routes.
2. Check iptables configurations for any blockages or misconfigurations that might be affecting internet access within the VMs/containers.
3. Look into Ubuntu-related threads to compare similar issues with different operating systems to determine if it's a configuration problem.
4. Research networkmanager, libvirt, and docker to better understand their interplay in managing networking aspects of virtual environments and containers.
5. Investigate possible solutions by modifying configurations based on user experience, documentation, and other online resources.

## Resolving the Issue: The Solution Found
After a thorough examination of different potential causes and attempting several fixes, I stumbled upon an effective solution for the networking issues in VMs and containers. By adding `virbr0` and `docker0` as unmanaged interfaces within networkmanager configuration, I was able to successfully manage internet connectivity across these environments without interference from libvirt or docker. 

Add the following line to `configuration.nix`:
```nix
networking.networkmanager.unamaged = [ "virbr0" "docker0" ];
```

For other distributions using NetworkManager, see [NetworkManager.conf(5)](https://linux.die.net/man/5/networkmanager.conf) and [RedHat documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/configuring-networkmanager-to-ignore-certain-devices_configuring-and-managing-networking):
```ini
[keyfile]
unmanaged-devices=interface-name:virbr0;interface-name:docker0
```

## Key Learnings & Takeaways:
- Virtual bridges should be managed automatically by software like libvirt; manual configurations could lead to unexpected consequences and complications in networking settings.
- Adding another bridge is not a suitable solution for resolving connectivity issues within VMs/containers, as this may create additional complexity and introduce potential conflicts with other network management systems.
- While libvirt automatically configures dnsmasq to establish internet access in virtual machines, it's essential to understand how these components interact to maintain a stable connection across different environments.
- It is crucial for users facing similar networking issues to approach the problem from multiple angles and carefully evaluate potential solutions before making any changes. This ensures an informed decision-making process that prioritizes stability, security, and usability within their computing environment.

## A question remains
Why did the problem surface only now? I have been using NetworkManager for a few years. Maybe something changed on their side. 
