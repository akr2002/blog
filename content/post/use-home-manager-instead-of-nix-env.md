---
title: "Use home-manager Instead of nix-env"
date: 2022-09-01T12:47:47+05:30
lastmod: 2022-09-01T12:47:47+05:30
draft: false
keywords: [nix home-manager nix-env]
description: ""
tags: []
categories: []
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
`home-manager` allows reprodicuble builds compared to `nix-env`. You can port your configuration to any machine and expect it to work the same.
<!--more-->
This post assumes you are on a non-NixOS Linux system.
# Getting nix
Nix can be installed via a simple shell script provided on [their website](https://nixos.org/download.html). Just use your judgement and follow the prompts.


# Installing `home-manager`
The configuration is stored in `~/.config/nixpkgs/home.nix`. Run `home-manager switch` to have changes take effect.

Export `NIX_PATH` to your shell
```bash
export NIX_PATH=$HOME/.nix-defexpr/channels:/nix/var/nix/profiles/per-user/root/channels${NIX_PATH:+:$NIX_PATH}
```
See [issue#2033](https://github.com/NixOS/nix/issues/2033) and [Nix discourse](https://discourse.nixos.org/t/where-is-nix-path-supposed-to-be-set/16434/8).

Now add unstable nix channel
```bash
 nix-channel --add https://github.com/nix-community/home-manager/archive/master.tar.gz home-manager
```
Or latest stable (as of writing this post) 22.05, if you prefer it
```bash
 nix-channel --add https://github.com/nix-community/home-manager/archive/release-22.05.tar.gz home-manager
```
And update
```bash
nix-channel --update
```

Install Home Manager
```bash
nix-shell '<home-manager>' -A install 
```
Home Manager requires your shell to source `~/.nix-profile/etc/profile.d/hm-session-vars.sh`. It can be easily done by setting `programs.bash.enable = true;` (of course, here bash is just an example). This way, Home Manager will manage your shell configuration. However, you will need to migrate your bashrc configurations over to Home Manager, which might take some time. An easier way out would be to move your existing bashrc and have Home Manager use that.
```
{ pkgs, ...}: {
  programs.bash = {
    enable = true;
    bashrcExtra = ''
      . ~/oldbashrc
    '';
  };
}
```

Since we are on a non-NixOS Linux system, Home Manager can automatically set some environment variables that will ease the usage of software installed with nix.
```
{pkgs, ...}: {
  targets.genericLinux.enable = true;
}
```

Now Home Manager is ready to use. There are many ways to install and configure packages using nix. Putting them here will make this post quite exhaustive. Check the links in the references as they go deeper and have up-to-date docs and examples.

# References
[NixOS Wiki - Home Manager](https://nixos.wiki/wiki/Home_Manager)

[Home Manager Manual](https://rycee.gitlab.io/home-manager/)
