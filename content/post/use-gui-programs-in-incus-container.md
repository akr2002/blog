---
title: "Use GUI Programs in Incus Container"
date: 2024-04-28T09:05:07+05:30
lastmod: 2024-04-28T09:05:07+05:30
draft: false
keywords: [gui, incus]
description: ""
tags: [incus]
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

If you've ever faced the challenge of running graphical user interface (GUI) programs
within a container, fear not! While it may seem like a daunting task at first, with this
comprehensive guide, we'll walk through the process using Incus Container. We will set up
and configure your environment to run GUI-based applications seamlessly in a virtualized
space. Let's get started!

<!--more-->

## Create a GUI profile

To begin, you must create a new profile specifically for managing GUI programs within the
Incus container. This can be done using the following command:

```bash
incus profile create gui
```

And edit with `incnus profile edit gui`. Throw the following contents in and save it.

```yaml
config:
  environment.DISPLAY: :0
description: ""
devices:
  X0:
    bind: container
    connect: unix:@/tmp/.X11-unix/X0
    listen: unix:@/tmp/.X11-unix/X0
    security.gid: "1000"
    security.uid: "1000"
    type: proxy
  mygpu:
    type: gpu
name: gui
```

We are using host's display so we will be using sockets.

## Add NixOS configuration

Now add the following in `configuration.nix`, rebuild and switch.

```nix
users.users.root = {
  subUidRanges = [
    {
      count = 1000;
      startUid = 1000;
    }
  ];
  subGidRanges = [
    {
      count = 1000;
      startGid = 1000;
    }
  ];
};
```

This update enables the matching of users and groups between your host machine and
container.

## Configure default Incus settings

Create or edit the `~/.config/incus/default.conf` file to provide default settings tailored specifically for running GUI applications within an Incus container. Add these configurations to your `default.conf`:

```conf
## For regular unpriv containers:
#incus.id_map = u 0 100000 65536
#incus.id_map = g 0 100000 65536
## For GUI upriv containers
incus.idmap = u 0 100000 1000
incus.idmap = g 0 100000 1000
incus.idmap = u 1000 1000 1
incus.idmap = g 1000 1000 1
incus.idmap = u 1001 101001 64535
incus.idmap = g 1001 101001 64535
incus.mount.entry = /dev/dri dev/dri none bind,optional,create=dir
incus.mount.entry = /dev/snd dev/snd none bind,optional,create=dir
incus.mount.entry = /tmp/.X11-unix tmp/.X11-unix none bind,optional,create=dir
incus.mount.entry = /dev/video0 dev/video0 none bind,optional,create=file
```

These configurations allow for the proper mapping of GUI applications within Incus and
provide mounting entries that correspond to essential devices required by these programs
(e.g., graphics card drivers).

On host machine, run

```bash
xhost +local:
```

Add your container to `gui` profile:

```bash
incus profile add <container> gui
```

## Running X Server Inside Your Container:

Now that you've set up your profile, it's time to install an X server inside your container to render graphical elements on the GUI applications. The installation process may vary depending on your distribution and version. For example, if using Arch Linux, run these commands:

```bash
yay -S xorg-server xorg-xauth xorg-drivers mesa-utils
```

This will install X server components required to render the GUI elements for your
applications within Incus.

## Executing a GUI Program in Your Container

Finally, let's test our setup by running an example GUI program inside the container with the appropriate user permissions. To do this, use the following command:

```bash
incus exec <container> --user 1000 -- glxgears
```

After executing the above command, you should see a window displaying spinning gears from
the GLXGEARS program - indicating that your GUI application is now successfully running
within Incus Container.

Happy hacking!
