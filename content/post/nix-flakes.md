---
title: "Nix Flakes - A Way to Manage Nix Projects"
date: 2023-06-18T12:12:38+05:30
lastmod: 2023-06-18T12:12:38+05:30
draft: false
keywords: [nix nix-flakes]
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

Nix is a powerful package manager that allows you to build, run, and deploy software in a reproducible and declarative way. However, until recently, Nix had some limitations when it came to managing dependencies, composing projects, and delivering software to users. In this blog post, we will introduce nix flakes, a new experimental feature that aims to solve these problems and improve the usability and composability of Nix.
<!--more-->

## Nix flakes explained

A flake is a source tree (such as a Git repository) that contains a file named `flake.nix` in its root directory. This file provides a standardized interface to Nix artifacts such as packages, NixOS modules, or Nix functions. A flake can have dependencies on other flakes, which are specified using a URL-like syntax. For example, `github:NixOS/nixpkgs` refers to the nixpkgs flake hosted on GitHub.

A flake also has a lock file named `flake.lock` that pins the exact revisions of its dependencies to ensure reproducible evaluation. The lock file can be updated programmatically using the `nix flake update` command.

Flakes are evaluated in a hermetic way, meaning that they don't depend on any external factors such as environment variables, command-line arguments, or the system type. This makes them more reliable and portable across different platforms and environments.

## Using nix flakes

To use nix flakes, you need to install an experimental version of Nix that supports them. You can get it from nixpkgs by running:

```bash
nix-shell -I nixpkgs=channel:nixos-23.05 --packages nixUnstable
```

You also need to enable the experimental features in your `~/.config/nix/nix.conf` file by adding:

```nix
experimental-features = nix-command flakes
```

Once you have nix flakes enabled, you can create a new flake by running:

```bash
nix flake init
```

This will generate a `flake.nix` file with some basic attributes:

```nix
{
  description = "A very basic flake";

  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";

  outputs = { self, nixpkgs }: {

    # A Nix package
    packages.x86_64-linux.hello = nixpkgs.legacyPackages.x86_64-linux.hello;

    # A NixOS module
    nixosModules.hello =
      { pkgs, ... }: {
        environment.systemPackages = [ pkgs.hello ];
      };
  };
}
```

The `description` attribute is a string that describes the flake. The `inputs` attribute is an attribute set of all the dependencies of the flake. In this case, we only have one dependency: the nixpkgs flake. The `outputs` attribute is a function that takes an attribute set of all the realized inputs and returns another attribute set with the artifacts produced by the flake.

In this example, we have two outputs: a Nix package named `hello` and a NixOS module named `hello`. The package is defined using the `nixpkgs.legacyPackages` attribute, which provides access to the packages defined in nixpkgs. The module is defined as a function that takes `pkgs` and other arguments and returns a NixOS configuration.

To build the package, we can run:

```bash
nix build .#hello
```

This will build the hello package defined in the current flake (denoted by `.`) and store it in the Nix store. We can also run it directly by using:

```bash
nix run .#hello
```

This will build and run the hello package without installing it.

To use the module, we can create a `configuration.nix` file that imports it:

```nix
{ config, pkgs, ... }:

{
  imports = [ ./flake.nix ];

  # Other configuration options...
}
```

Then we can build and activate our system configuration by running:

```bash
nixos-rebuild switch --flake .
```

This will use the current flake as the source of our system configuration and apply it to our machine.

## How to compose nix flakes?

One of the main advantages of nix flakes is that they allow us to compose different Nix projects in a modular and declarative way. For example, suppose we want to use a flake that provides some useful Nix functions, such as `flake-utils`:

```nix
{
  description = "A flake with some useful Nix functions";

  inputs.flake-utils.url = "github:numtide/flake-utils";

  outputs = { self, nixpkgs, flake-utils }: {

    # A Nix package
    packages.x86_64-linux.hello = nixpkgs.legacyPackages.x86_64-linux.hello;

    # A NixOS module
    nixosModules.hello =
      { pkgs, ... }: {
        environment.systemPackages = [ pkgs.hello ];
      };

    # A function that creates a flake from a simple Nixpkgs overlay
    overlayFlake = flake-utils.lib.simpleFlake;
  };
}
```

Here we have added a new input for the `flake-utils` flake and a new output for the `overlayFlake` function. This function takes a simple Nixpkgs overlay and returns a flake that provides packages and apps based on it.

We can use this function to create another flake that uses an overlay to customize some packages:

```nix
{
  description = "A flake that uses an overlay to customize some packages";

  inputs.nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
  inputs.flake-with-functions.url = "/path/to/flake-with-functions";

  outputs = inputs@{ self, nixpkgs, flake-with-functions, ... }:
    flake-with-functions.overlayFlake {
      # An overlay that adds a suffix to the hello package
      overlay = final: prev: {
        hello = prev.hello.overrideAttrs (old: {
          name = "${old.name}-customized";
        });
      };

      # Other arguments passed to simpleFlake
      inherit inputs;
      name = "customized-hello-flake";
    };
}
```

Here we have created a new flake that depends on the `nixpkgs` flake and the `flake-with-functions` flake. We use the `overlayFlake` function from the latter to create a flake that provides a customized version of the hello package. We pass an overlay that adds a suffix to the package name, as well as other arguments required by the `simpleFlake` function.

We can build and run the customized hello package by using:

```bash
nix run .#hello
```

This will show:

```bash
Hello, world!-customized
```

We can also use the `niv` tool to manage our flake dependencies in a more convenient way. For example, we can run:

```bash
niv init -f
```

This will create a `sources.nix` file that contains the URLs and revisions of our flake inputs. We can then use this file in our `flake.nix` file by importing it:

```nix
{
  description = "A flake that uses an overlay to customize some packages";

  inputs = import ./sources.nix;

  outputs = inputs@{ self, nixpkgs, flake-with-functions, ... }:
    flake-with-functions.overlayFlake {
      # An overlay that adds a suffix to the hello package
      overlay = final: prev: {
        hello = prev.hello.overrideAttrs (old: {
          name = "${old.name}-customized";
        });
      };

      # Other arguments passed to simpleFlake
      inherit inputs;
      name = "customized-hello-flake";
    };
}
```

We can also use `niv` to update our dependencies by running:

```bash
niv update
```

This will fetch the latest revisions of our inputs and update the `sources.nix` file accordingly.

## Conclusion

Nix flakes are a new experimental feature that improve the reproducibility, composability, and usability of Nix projects. They provide a standardized interface to Nix artifacts, a way to specify and pin dependencies, and a hermetic evaluation model. They also enable new ways to deliver software to users, such as using `nix profile` or `nix bundle`. Flakes are still under development and may change in the future, but they are already usable and provide many benefits over the traditional Nix approach.

If you want to learn more about nix flakes, you can check out the following resources:

- [The NixOS RFC for flakes](https://github.com/NixOS/rfcs/)
- [The Nix manual for flakes](https://nixos.org/manual/nix/unstable/command-ref/new-cli/nix3-flake.html)
- [The NixOS wiki for flakes](https://nixos.wiki/wiki/Flakes)

## References
- Flakes - NixOS Wiki. https://nixos.wiki/wiki/Flakes.
- Nix Flakes, Part 1: An introduction and tutorial - Tweag. https://www.tweag.io/blog/2020-05-25-flakes/.
- Practical Nix Flakes - Serokell Software Development Company. https://serokell.io/blog/practical-nix-flakes.
- A Tour of Nix Flakes | Mattia Gheda. https://ghedam.at/a-tour-of-nix-flakes.
