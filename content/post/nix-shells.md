---
title: "Nix Shells: What they are and how to use them"
date: 2023-06-24T11:21:00+05:30
lastmod: 2023-06-24T11:21:00+05:30
draft: false
keywords: [nix nix-shell "nix shell" "nix develop"]
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

Nix is a powerful tool for managing software dependencies and reproducible environments. One of the features of Nix is the ability to create and enter interactive shells that provide a specific set of tools and variables for a given project or task. In this post, I will explain what are the different types of Nix shells, how they work, and how to use them effectively.

<!--more-->
## nix-shell

`nix-shell` is the original command for creating and entering Nix shells. It takes a Nix expression as an argument, which can be either a file name (such as `shell.nix` or `default.nix`), a derivation (such as `nixpkgs#hello`), or an arbitrary expression (such as `-p python3`). `nix-shell` then builds the expression and starts a Bash subshell with the environment variables and shell functions set up according to the expression.

For example, if you have a `shell.nix` file in your project directory that looks like this:

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
  buildInputs = [
    pkgs.python3
    pkgs.python3Packages.numpy
    pkgs.python3Packages.pandas
  ];
}
```

You can run `nix-shell` in the same directory and get a shell with Python 3 and its packages available:

```bash
$ nix-shell
[nix-shell:~/my-project]$ python3
Python 3.10.11 (main, Apr  4 2023, 22:10:32) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import numpy
>>> import pandas
>>> 
```

You can also use `nix-shell` to run a single command in a Nix shell, without entering it interactively. For example, you can run `nix-shell -p curl --run "curl https://nixos.org"` to download the NixOS homepage using the `curl` package from Nixpkgs.

`nix-shell` has some options that modify its behavior, such as:

- `--pure`: Clear the entire environment (except those specified with --keep) before entering the shell. This ensures that the shell is isolated from any impurities from the parent environment, such as system packages or environment variables.
- `--run`: Run the specified command inside the shell, instead of starting an interactive shell.
- `--command` or `-c`: Similar to --run, but allows multiple commands to be executed sequentially.
- `--keep` or `-k`: Keep the specified environment variable when entering the shell.
- `--arg`: Pass an argument to the Nix expression that defines the shell.
- `--argstr`: Similar to --arg, but passes a string argument.

## nix shell

`nix shell` is a newer command that is part of the new Nix user interface introduced in Nix 2.0. It is similar to `nix-shell`, but has some differences and advantages. `nix shell` takes one or more installables as arguments, which can be either flake outputs (such as `.#hello` or `nixpkgs#firefox`) or legacy packages (such as `hello` or `firefox`). `nix shell` then builds and installs these installables into a temporary profile, and starts a Bash subshell with these installables available in `$PATH`.

For example, you can run `nix shell nixpkgs#python3 nixpkgs#python3Packages.numpy --command "python3 -c 'import numpy; print(numpy.__version__)'"` to print the version of numpy using Python 3 from Nixpkgs.

`nix shell` has some advantages over `nix-shell`, such as:

- It supports flakes, which are a new way of defining and composing Nix projects with improved reproducibility and composability.
- It does not require a `shell.nix` or `default.nix` file to exist in the current directory. You can specify any installable from any source as an argument.
- It does not use Bash-specific features to set up the environment, which makes it more portable and compatible with other shells.
- It does not leak any build-time dependencies into the shell environment, which makes it more lightweight and minimal.

However, `nix shell` also has some limitations compared to `nix-shell`, such as:

- It does not support arbitrary Nix expressions as arguments. You can only use installables that are defined in flakes or in Nixpkgs.
- It does not support setting up environment variables or shell functions that are not related to the installables. For example, you cannot use `nix shell` to set up a PostgreSQL service or a Python virtual environment.
- It does not support running a specific phase of a derivation, such as configure or build. You can only run the final result of the derivation.

## nix develop

`nix develop` is another new command that is part of the new Nix user interface. It is similar to `nix shell`, but instead of providing a minimal environment with only the installables in `$PATH`, it provides a full-fledged build environment that is nearly identical to what Nix would use to build the installables. Inside this shell, environment variables and shell functions are set up so that you can interactively and incrementally build your project.

For example, if you have a `flake.nix` file in your project directory that looks like this:

```nix
{
  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";

  outputs = { self, nixpkgs }: {

    packages.x86_64-linux.hello = nixpkgs.legacyPackages.x86_64-linux.hello;

    devShell.x86_64-linux = nixpkgs.legacyPackages.x86_64-linux.mkShell {
      buildInputs = [
        nixpkgs.legacyPackages.x86_64-linux.hello
      ];
    };

  };
}
```

You can run `nix develop` in the same directory and get a shell with the build environment of the hello package.

You can also use `nix develop` to run a single command in a Nix shell, without entering it interactively. For example, you can run `nix develop .#hello --command "hello"` to run the hello program from the flake output.

`nix develop` has some advantages over `nix-shell`, such as:

- It supports flakes, which are a new way of defining and composing Nix projects with improved reproducibility and composability.
- It does not require a `shell.nix` or `default.nix` file to exist in the current directory. You can specify any flake output as an argument.
- It does not use Bash-specific features to set up the environment, which makes it more portable and compatible with other shells.
- It allows you to run specific phases of a derivation, such as configure or build, by using options like `--configure` or `--build`.

However, `nix develop` also has some limitations compared to `nix-shell`, such as:

- It does not support arbitrary Nix expressions as arguments. You can only use flake outputs that are derivations.
- It does not support installing multiple installables into the same shell. You can only use one flake output as an argument.
- It does not support setting up environment variables or shell functions that are not related to the derivation. For example, you cannot use `nix develop` to set up a PostgreSQL service or a Python virtual environment.

## Conclusion

In this post, I have explained what are the different types of Nix shells, how they work, and how to use them effectively. `nix-shell` is the original command for creating and entering Nix shells based on arbitrary Nix expressions. `nix shell` is a newer command for creating and entering minimal shells based on installables.

## References
- [Nix shells - tips, tricks, and best practices - NixOS Discourse](https://discourse.nixos.org/t/nix-shells-tips-tricks-and-best-practices/22332).
- [nix develop - Nix Reference Manual - NixOS Linux](https://nixos.org/manual/nix/unstable/command-ref/new-cli/nix3-develop.html).
- [Development environment with nix-shell - NixOS Wiki](https://nixos.wiki/wiki/Development_environment_with_nix-shell).
