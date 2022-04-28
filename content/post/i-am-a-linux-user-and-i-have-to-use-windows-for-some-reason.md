---
title: "I am a Linux user and I have to use Windows for some reason"
date: 2021-02-14T05:32:59Z
lastmod: 2022-04-28T05:32:59Z
draft: false
keywords: [linux windows wsl]
description: ""
tags: [windows]
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

<!--more-->

Some tips to make your work easier. Believe me, Windows is not as alien thing as you want to beleive. Most of the work can be done form command line (as you usually would on Linux). Applies to regular Windows users as well.

This is essentially one of the four blog posts. Don't worry, you will find the other three as you read.

## Do not let Windows Update get in your way
I have seen a lot of people complain Windows Update reboots in the middle of their work. Although the situatiojn has improved quite a lot in recent days, still, you would want to avoid this possibility as much as possible. 

Now to save myself some time, I will link my blog post where I cover how to "fix" automatic updates and install updates manually as per your convenience.

[How to have Windows 10 NOT get in your way]({{< ref "/post/how-to-have-windows-10-not-get-in-your-way.md" >}})

Ofcourse, by following the steps the responsibility of managing the security of your computer falls entirely on you.

## Install a package manager
COnsidering the target audience of this blog post, I think I do not need to emphasize on the importance of package managers and the convenience they provide.

In this section I will be covering *scoop* and *chocolatey*. Both package managers will cover most of your stuff you will usually need.

### Scoop 
Scoop is a package manager that installs packages without polluting your path. By default, it always fetches portable versions so you will rarely need admin privilege (UAC) to install a package and rest assured the installed packages will not mess with registry.

Before installing Scoop you must have PowerShell 5 (or later) and .NET Framework 4.5 (or later) installed. You can also read their [documentation](https://github.com/lukesampson/scoop/wiki).

#### Installation
Follow the instructions here or head to their [GitHub repo](https://github.com/lukesampson/scoop).

Open PowerShell by pressing **Win+Q** > **powershell**.

Change execution policy in order to run the installation script
```powershell
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```
{{% center %}}
{{% figure src="/img/scoop-install.png" title="Scoop install" alt="Screenshot of above command where it prompts the user to enter an option to change execution policy" %}}
{{% /center %}}

Enter **Y** when prompted.

Paste the following line to download and rin unstallation script
```powershell
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
```

If everything works as expected then you will see the message *Scoop was installed successfully!* in your console.

By default, Scoop installs all your programs under `~/scoop`. If you wish to install Scoop to a custom directory then set `SCOOP` environment variable to your preferred location and run the installer.
```powershell
$env:SCOOP='D:\your\new\location\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')
```

Packages are categorised into various buckts (main, extra, games, etc.) so you will need to add them as needs arise. A ;list if (and details about) buckets can be found in their GitHub repo linked above.

By default, the *main* bucket is used. To get more packages, you will need to add the *extras* bucket. To add, run
```powershell
scoop install git 
scoop bucket add extras
```

#### Usage
Search for packages with
```powershell
scoop search <pacage name>
```

Get package information with
```powershell
scoop list
```

Get package information with
```powershell
scoop info <package name>
```

See which packages can be updated with
```powershell
scoop status
```

Update packages with
```powershell
scoop update 
```

Uninstall packages with
```powershell
scoop uninstall <package name>
```

In case a package is not available on Scoop, you might want to check Chocolatey.

### Chocolatey
Normally, Chocolatey installs packages in their preferred locations, requires admin privileges (which allows packages to clutter registry, run at startup and what not). I will cover both normal installation and non-admin install. Detailed instructions are available at [Chocolatey docs](https://docs.chocolatey.org/en-us/choco/setup).

#### Normal install
Run the following in elevated PowerShell 
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

{{% center %}}
{{% figure src="/img/chocolatey-install.png" title="Chocolatey install" alt="Screenshot of above command in execution" %}}
{{% /center %}}

Being the careful person you are, **always** review **any** script you download before running it on your computer.

#### Installing to a custom location
Create a machine/user level environment variable named `ChocolateyInstall`, set it to your preferred location and make sure the environment varible is set globally and is visible to PowerShell. Install as normal.

#### Non-admin install
Copy `ChocolateyInstallNonAdmin.ps1` form their [website](https://docs.chocolatey.org/en-us/choco/setup#non-administrative-install). Modify it to suit your needs and run
```powershell
.\ChocolateyInstallNonAdmin.ps1
```

{{% center %}}
{{% figure src="/img/chocolatey-non-admin-install.png" title="Chocolatey non-admin install" alt="Screenshot of above command in execution" %}}
{{% /center %}}

#### Usage
Search for packages with (alias for `list`)
```powershell
choco search <package>
```

See package information with
```powershell
choco info <package>
```

Install packages with
```powershell
choco install <package>
```

Uninstall packages with
```powershell
choco uninstall <package>
```

Update packages with
```powershell
choco upgrade <package>
```

Update all packages with
```powershell
choco upgrade *
```

And a lot more, see [docs](https://docs.chocolatey.org/en-us/choco/commans/).

## Install the packages you are familiar with
```powershell
scoop install wget curl nano vim less git bat 
```

Ofcourse, this is just an example

Personally, I am not a fan of Adobe Reader. So I use Okular
```powershell
scoop install okular 
```

## Get yourself a decent shell 
There is not much option. We will be installing PowerShell Core. It is activel yupdated and does not have nasty alias.
```powershell
scoop install pwsh
```

## Time to install a decent terminal emulator
There are quite a few nice ones, namely *cmder*, *ConEmu*, and *Windows Terminal*. The three are highly customisable. There are also not so popular ones (*hyper*, *terminus*). Install Windows Terminal with
```powershell
scoop install windows-terminal
```

You can split the terminal horizontally with **Alt+Shift+-** and vertically with **Alt+Shift++**

{{% center %}}
{{% figure src="/img/windows-terminal-split.png" title="Allow me to flex for a moment" alt="Screenshot of Windows Terminal with three split sessions, one on left and two on right" %}}
{{% /center %}}

You might also want to make Windows Terminal beautiful and PowerShell fancier as you normally would with fish or zsh. I have covered it extensively in my other post.

[Make PowerShell fancier with Windows Terminal]({{< ref "post/make-powershell-fancier-with-windows-terminal.md" >}})

To change Windows Terminal settings, press **Ctrl+,** or open it directly with
```powershell
vim "~/AppData/Local/Microsoft/Windows Terminal/settings.json"
```

Hit Tab, arrow keys and Enter while typing to help yourself

{{% center %}}
{{% figure src="/img/wt-suggestions.png" title="Autocomplete suggestions" alt="Screenshot of an incomplete command with autocomplete suggestions" %}}
{{% /center %}}

You do not need Azure Cloud Shell so set *hidden* attribute to `true`.

{{% center %}}
{{% figure src="/img/wt-acs.png" title="It won't bother anymore" alt="Screenshot of the JSON snippet where hidden attribute sis set to true" %}}
{{% /center %}}

End result:

{{% center %}}
{{% figure src="/img/wt-screenfetch.png" title="Screenfetch time" alt="Screenshot of command screenfetch" %}}
{{% /center %}}

## Set new defaults as you use Windows
Say you want to open a file and are prompted to choose an application to oprn a file and you do not find your preferred program.

{{% center %}}
{{% figure src="/img/preferred-application.png" alt="Screenshot of a prompt listing various programs to open a C source file" %}}
{{% /center %}}

Click on *More apps*. If you still do ont find it then clikc on *Look for another app on this PC*. Navigate to your installed application (which in my case id `~/scoop/apps/notepadplusplus/current/notepad++.exe`), select your executable and click *Open*.

{{% center %}}
{{% figure src="/img/select-preferred-application.png" title="Always select symlink" alt="Screenshot of the final step to select a preferred application to open a file type" %}}
{{% /center %}}

**Note:** When selecting an application under scoop, always select the one under *current* folder (you will see an arrow i its icon). It is a shorcut (symlink) to the latest version of the package. Scoop links *current* to the latest version so you do not have to worry about updating the defaults after every update.

## Show file extensions in File Explorer
I don't know why it is disabled by default but it is definitely a good feature.

Click on *View* tab and check *File name externsions* box.

{{% center %}}
{{% figure src="/img/file-ext.png" title="Definitely a good feature" alt="Screenshot of View tab where File name externsions box is checked" %}}
{{% /center %}}

## Connect to an FTP server using FIle Explorer
This works the same as Nautilus or Dolphin. Press **Win+E** > **Ctrl+L**. Now you will be presented to the address bar. Enter FTP address and continue as normal.

## See all STart menu apps in FIle Explorer
Honestly, I haven't seen its use case except [one]({{< ref "/post/abort-a-shutdown-restart-and-start-working-again.md" >}}) but it is deifnitely neat. Press **Win+E** > **Ctrl+L**, type `shell:AppsFolder` and hit Enter.

{{% center %}}
{{% figure src="/img/app-list.png" title="Looks neat" alt="List of installed apps in File Explorer" %}}
{{% /center %}}

## Copy the output of a file yo a clipboard
Your normal output redirection you usually do in Linux works in PowerShell too. So instead of that I am just covering copying output to clipboard. Pipe the output to `Set-Clipboard`.
```powershell
your-command | Set-Clipboard
```

{{% center %}}
{{% figure src="/img/set-clipboard.png" title="Get-Content is same as cat for spitting file contents" alt="Content of copyStr.c is output which is piped to Set-Clipboard" %}}
{{% /center %}}

## Use Tab completion frequently
Being a person with weak memory as I am, this is definitely one of those features which must be enabled. Helps a ton.

{{% center %}}
{{% figure src="/img/tab-completion.png" title="Awesome, isn't it?" alt="If you type an incomplete command and hit tab, it will list all its acceptable parameters. Hitting tab again or arrow keys will allow you to select a different command" %}}
{{% /center %}}

## SSH into a remote computer
Now you no longer need to use PuTTY to SSH into a remote computer. Enable SSH by pressing **Win+I** > **Apps** > **Apps &amp; Features** > **Optional Features** > **Add a Feature**. Select *OpenSSH Client** and **OpenSSH Server* and click *Install*. (Reboot if prompted), open PowerShell Core and ssh as normal.

{{% center %}}
{{% figure src="/img/install-ssh.png" title="I seem to have forgotten a command line equivalent" alt="Screenshot of Settings page that lists optional features. The checkbox next to OpenSSH Server is selected" %}}
{{% /center %}}

Despite all these, you have WSL2 too which I have covered in my other post.

[Install and configure Arch Linux in WSL/WSL2 with GUI]({{< ref "/post/install-and-configure-arch-linux-inwsl-wsl2-with-gui.md" >}})

## Next step
Enjoy.
