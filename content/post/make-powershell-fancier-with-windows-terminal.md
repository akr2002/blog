---
title: "Make Powershell Fancier With Windows Terminal"
date: 2020-03-22T11:33:38+05:30
lastmod: 2022-03-11T11:33:38+05:30
draft: false
keywords: [powershell, windows-terminal]
description: ""
tags: ["powershell", "windows terminal"]
categories: ["windows"]
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
*because why not*

# Install PowerShell Core
Or you can continue using PowerShell 5.1 if you wish.

## From GitHub
Grab the installer from their [releases](https://github.com/PowerShell/PowerShell/releases) page. Once downloaded, double-click the installer and follow the instructions. 

## Using Scoop
Enter the line in PowerShell:

```
scoop install pwsh
```

Instructions to install Scoop can be found at [scoop.sh](https://scoop.sh/).

## Using Chocolatey
Enter the line in elevated PowerShell:

```
choco install pwsh
```

Instructions to install Chocolatey can be found at [chocolatey.org](https://chocolatey.org/install).

# Install Windows Terminal
Get it from [Store](https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701) or from [GitHub releases](https://github.com/Microsoft/Terminal/releases). I recommend getting it from Store as it can be updated automatically.

Or you can install via Chocolatey by entering
```
choco install microsoft-windows-terminal
```
Again, in elevated PowerShell.

If you are using Scoop then it would be
```
scoop install windows-terminal
```

# The interesting part begins.
Next, open Windows Terminal. Once there, use the drop down arrow to open PowerShell Core.

{{% center %}}
{{% figure src="/img/powershell/tab-menu.png" title="The shells listed depend on the shells installed on your computer." alt="A drop down list of shells, settings, feedback and about" %}}
{{% /center %}}

As per the instructions [here](https://github.com/JanDeDobbeleer/oh-my-posh), install `posh-git` and `oh-my-posh`. It is assumed that you have [git](https://git-scm.com/downloads) installed (you can use Chocolatey or Scoop if you wish).

```
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

Next, get [PSReadline](https://docs.microsoft.com/en-us/powershell/module/psreadline/?view=powershell-7)

```
Install-Module -Name PSReadLine -AllowPrerelease -Scope CurrentUser -Force -SkipPublisherCheck
```

Now, add the folllowing lines to your `$PROFILE` by running `vim $PROFILE`. Please note that Notepad also works.

```powershell
Import-Module posh-git
Import-Module oh-my-posh
Set-PoshPrompt ys

# Show navigable manu of all options on hitting Tab
Set-PSReadlineKeyHandler -Key Tab -Function MenuComplete

# Autocompletion for arrow kets
Set-PSReadlineKeyHandler -Key UpArrow -Function HistorySearchBackward
Set-PSReadlineKeyHandler -Key DownArrow -Function HistorySeachForward
```

Line 3 sets theme. You can get a list of themes [here](https://github.com/JanDeDobbeleer/oh-my-posh).

# Time to get a better font
Those boxes don't look quite nice.

[Cascadia Code](https://github.com/microsoft/cascadia-code/releases) is my personal favourite. Download the fonts and change the font face to "Cascadia Code PL" in `settings.json` (it can be opened by pressing `Ctrl+,`, if you hadn't noticed already).
```json
"fontFace": "Cascadia Code PL";
```
Or you can install other fonts that support PowerLine Glyphs from [Nerd Fonts](https://nerdfonts.com/)

# Add a background
One of the fun things about Windows Terminal is that you can set backgrounds and opacity or apply acrylic blur, which will be covered in the next section.

Open `settings.json` and add the following lines

```json
"backgrounfImage": "path/to/image",
"backgroundOpacity": 0.5,
"backgroundImageStretchMode": "fill",
```

# Use acrylic
Remove the lines used to set the background and add the following lines

```json
"useAcryllic": true,
"acryllicOpacity": 0.8,
```

# Next Step
Enjoy.
{{% center %}}
{{% figure src="/img/powershell/final.png" title="Final result" alt="Screenshot of Windows Terminal after applying the previous steps" %}}
{{% /center %}}