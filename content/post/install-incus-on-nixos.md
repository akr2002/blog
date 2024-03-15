---
title: "Install Incus on Nixos"
date: 2024-02-29T19:14:10+05:30
lastmod: 2024-03-16T00:21:10+05:30
draft: false;
keywords: [incus, nixos]
description: ""
tags: [incus, nixos]
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
Incus, a manager and hypervisor for system containers (LXC) and virtual machines (QEMU), is an excellent tool for managing and orchestrating your applications and services. It is a fork of LXD by the original maintainers.
<!--more-->
I found the documentation regarding NixOS lacking and thought I should put it somewhere for future reference. If you have experience with LXD, it will mostly be similar but expect things to get different as time passes.

## Installation
Incus is already present in `nixpkgs` and can be installed by adding 
```nix
virtualisation.incus.enable = true
``` 
to your `configuration.nix`. Consider adding yourself to `incus-admin` group to avoid using `sudo` every time. It can be done by
```nix
users.user.USER.extraGroups = [ "incus-admin" ];
```
Of course, replace `USER` with your username. 

You need IP forwarding for NAT'ing to work
```nix
boot.kernel.sysctl = {
    "net.ipv4.conf.all.forwarding" = true;
    "net.ipv4.default.forwarding" = true;
};
```

Enable kernel module for IP forwarding to work
```nix
boot.kernelModules = [ "nf_nat_ftp" ];
```

Set up a bridge
```nix
networking.bridges = { incusbr0.interfaces = []; };
```
This is used to provide NAT'd internet to the guest. It is manipulated directly by incus, so no need to specify any bridged interfaces here.

<s>Add firewall rules to enable networking in the container
```nix
networking.firewall.extraCommands = ''
    iptables -A INPUT incusbr0 -j ACCEPT
    iptables -A FORWARD -o incusbr0 -j ACCEPT
    iptables -A FORWARD -i incusbr0 -j ACCEPT
    iptables -A OUTPUT -o incusbr0 -j ACCEPT
'';
```
</s>

Incus on NixOS dropped `iptables` support and recommends using `nftables`. Enable `nftables` and add `incusbr0` to trusted interfaces.

```nix
networking.nftables.enable = true;
networking.firewall.trustedInterfaces = [ "incusbr0" ];
```
Enable lxcfs to use it

```nix
virtualisation.lxc.lxcfs.enable = true;
```

Now switch to the new configuration with
```nix
nixos-rebuild switch
```

## Setting up incus
Incus requires initial setup for networking and storage. It can be done interactively by running
```bash
incus admin init
```
List all available images
```bash
incus image list images:
```

Create a new image `alpine` based on Alpine Linux
```bash
incus launch images:alpine/3.19 alpine
```

Interact with the newly created image
```bash
incus exec alpine -- ash
```
This will drop you in an `ash` shell in the container.

You can copy containers by running
```bash
incus copy $CONTAINER1 $CONTAINER2
```

List containers
```bash
incus list
```

Stop container
```bash
incus stop $CONTAINER
```

Delete container
```bash
incus delete $CONTAINER
```

## Configuration
Launch a new container with resource constrants
```bash
incus launch images:alpine/3.19 alp1 --config limits.cpu=1 --config limits.memory=192MiB
```

Check configuration
```bash
incus config show alp1
```

Update configuration
```bash
incus config set alp1 limits.memory=128MiB
```
## Interaction
Run arbitrary commands
```bash
incus exec alpine -- apk update
```

Pull a file from container
```bash
incus file pull alpine/etc/hosts .
```

Push file back to the container
```bash
incus file push hosts alpine/etc/hosts
```

## Snapshots
Create a snapshot
```bash
incus snapshot create alpine alpine_snapshot
```

Restore the container to the snapshot
```bash
incus snapshot restore alpine alpine_snapshot
```

Delete the snapshot
```bash
incus delete alpine/alpine_snapshot
```

## References
1. [Howto setup LXD on NixOS with NixOS guest using unmanaged bridge network interface](https://discourse.nixos.org/t/howto-setup-lxd-on-nixos-with-nixos-guest-using-unmanaged-bridge-network-interface/21591)
2. [First steps with Incus](https://linuxcontainers.org/incus/docs/main/tutorial/first_steps/)
