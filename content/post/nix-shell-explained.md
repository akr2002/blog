---
title: "nix-shell Explained"
date: 2023-06-18T09:52:01+05:30
lastmod: 2023-06-18T09:52:01+05:30
draft: false
keywords: [nix nix-shell]
description: ""
tags: [nix]
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
Nix-shell is a powerful tool that allows you to create reproducible and isolated development environments for any project. In this blog post, I will show you how to use nix-shell to set up a development environment for a Node.js project.
<!--more-->
## What is nix-shell?

Nix-shell is a command that comes with Nix, a package manager and system configuration tool that takes a unique approach to managing software. Nix allows you to declare your dependencies in a file called `default.nix` or `shell.nix`, and then builds them in a sandboxed environment, ensuring that they are isolated from each other and from the rest of your system.

Nix-shell will build the dependencies of the specified derivation (a term used in Nix to refer to a package or a build process), but not the derivation itself. It will then start an interactive shell in which all environment variables defined by the derivation have been set to their corresponding values, and the script `$stdenv/setup` has been sourced. This means that you get an environment that closely resembles the one that Nix would use to build your project, but without actually building it.

This is useful for reproducing the environment of a derivation for development. For example, if you have a Node.js project that depends on some npm packages, you can use `nix-shell` to install those packages and run your project without affecting your global npm installation or other projects.

## How to use nix-shell?

To use nix-shell, you need to have Nix installed on your system. You can follow the instructions on [NixOS website](https://nixos.org/download.html) to install Nix on Linux, MacOS, or Windows.

Once you have Nix installed, you need to create a file called `default.nix` or `shell.nix` in your project directory. This file will contain the declaration of your dependencies and any other configuration options for your development environment. For example, here is a simple `shell.nix` file for a Node.js project:

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
name = "node-project";
buildInputs = [ pkgs.nodejs ];
shellHook = ''
echo "Welcome to node-project"
export NODE_ENV=development
'';
}
```
This file uses some Nix syntax and functions that may look unfamiliar at first, but don't worry, I will explain them briefly.

- The first line is a function argument that allows us to import the `nixpkgs` package set, which contains thousands of pre-built packages for various languages and tools. We can access these packages through the `pkgs` variable.
- The second line uses the `mkShell` function from `nixpkgs`, which is a helper function that creates a derivation suitable for nix-shell. It takes some arguments, such as:
- `name`: The name of the derivation.
- `buildInputs`: A list of packages that will be available in the nix-shell environment. In this case, we only need `nodejs`, which is provided by `nixpkgs`.
- `shellHook`: A bash script that will be run after `$stdenv/setup` has been sourced. This allows us to perform some initialisation specific to nix-shell, such as printing a welcome message or setting some environment variables.

After creating this file, we can run `nix-shell` in our project directory, and we will be dropped into a shell where we have access to `nodejs` and our custom environment variables. We can then run our project as usual, for example:
```bash
$ nix-shell
Welcome to node-project

[nix-shell:~/node-project]$ node -v
v18.16.0

[nix-shell:~/node-project]$ node index.js 
Hello from nix-shell

[nix-shell:~/node-project]$ exit
exit
```

As you can see, nix-shell allows us to easily create and enter a development environment for our project without affecting our system or other projects. We can also share our `shell.nix` file with other developers and ensure that they have the same environment as us.

## Conclusion

`nix-shell` is a powerful tool that allows you to create reproducible and isolated development environments for any project. In this blog post, I showed you how to use `nix-shell` to set up a development environment for a Node.js project. You can learn more about `nix-shell` and Nix in general by reading the Nix manual, the Nix pills, or the NixOS website. I hope you enjoyed this blog post and found it useful. Happy hacking!
