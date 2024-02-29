---
title: "Make Bash Prettier on NixOS"
date: 2024-02-29T14:32:11+05:30
lastmod: 2024-02-29T14:32:11+05:30
draft: false
keywords: [bash nixos]
description: ""
tags: [bash, nixos]
categories: [linux]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: false
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
Using `ble.sh` and Starship.
<!--more-->
Bash by itself is pretty basic. Syntax highlighting, auto-suggestion and history matching are pretty helpful features in any shell. Users might be worried about the bloat these features bring and add delay to the startup of the shell. 

I've been using these features for quite a while and have yet to see a performance hit. So it should be safe to assume you won't be having any of these problems either.

The reason why I am configuring bash instead of using other shells that have a better support these features is `nix develop` and `nix shell` expect bash and most of its functionalities are built around bash. So I do not recommend using a different shell for these.

## `ble.sh`
[ble.sh](https://github.com/akinomyoga/ble.sh) or _Bash Line Editor_ is a command line editor written in pure Bash and replaces GNU Readline. Although it is written in Bash, it relies on POSIX `stty` to set up TTY states before and after the execution of user commands. 

Installing `ble.sh` on NixOS is pretty straight forward as documented in the git repo.

```bash
# Spawn a development shell to bring in dependencies
nix develop nixpkgs#bash
# Bring build dependencies to path
nix shell nixpkgs#git nixpkgs#gnumake

# Try without installing
git clone --recursive --depth 1 --shallow-submodules https://github.com/akinomyoga/ble.sh.git
make -C ble.sh
source ble.sh/out/ble.sh

# Install to ~/.bashrc
git clone --recursive --depth 1 --shallow-submodules https://github.com/akinomyoga/ble.sh.git
make -C ble.sh install PREFIX=~/.local
echo 'source ~/.local/share/blesh/ble.sh' >> ~/.bashrc
```

Now add the following line to `home.nix`:
```nix
programs.bash = {
    bashrcExtra = ''
        . ~/.bashrc
    '';
};
```
Switch to the new configuration:
```bash
home-manager switch
```
Now the changes should persist across sessions. 

`ble.sh` brings syntax highlighting, auto-completion, auto-suggestion, menu-complete and more.

## Starship
[Starship](https://starship.rs) customizes shell prompt. The defaults are surprisingly sane for what it does. It is fast and does not get in the way. The prompt changes based on your directory. You can install it by adding it to `home.nix`:
```nix
home.packages = with pkgs; [ starship ];
```
Append the line to `~/.bashrc`
```bash
eval "$(starship init bash)"
```
Personally, I never felt the need to change it but you can choose from the [many existing configurations](https://starship.rs/config/). Oh, and have [Nerd Font](https://www.nerdfonts.com/) enabled. 
{{% figure src="/img/bash-nixos.png" alt="Screenshot of shell featuring Starship and ble.sh" %}}
