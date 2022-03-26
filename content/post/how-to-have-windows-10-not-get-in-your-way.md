---
title: "How to Have Windows 10 NOT Get in Your Way"
date: 2020-10-19T22:13:23+05:30
lastmod: 2022-03-25T22:13:23+05:30
draft: false
keywords: [windows powershell windows-update]
description: ""
tags: [windows windows-update]
categories: [windows]
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

Well you might have reasons to use Windows 10 and be without an alternative. In that case it is super important to have it NOT get in your way as should be the case. The biggest issues I see people face are forced updates, telemetry, and the computer slowing down over time. There are third-party solutions to all of these problems but here I will write about the ones you can fix yourself.

## "Fix" automatic updates
### Disable automatic updates
Microsoft has its own reasons to force an update, mostly for security issues and stuff and honestly, O recommend you to leave it that way if you have zero idea about what you are doing. But I have seen a lot of people stuck with Windows updating itself in a critical moment. Secondly, a lot of updates, as of late, tend to break more stuff than they (intend to) fix.

One way to have Winodws Update not automatically download updates is to set connection as metered. Go to **Settings > Network & Internet > Status > Properties** and toggle **Set connection as metered** switch to on. Now this will prevent Windows from doing a few things on its own. It will stop downloading updates, stop updating Microsoft Store apps and stop downloading the required drivers.

Now what? Now you have a computer that won't get in your way by updating itself at a critical momeny and not have it broken by installing an erraneous update. But this also leaves your PC wied open to security flaws. I mean it wasn't particularly immune before toggling the above setting but now it just became  easier. 

### Download updates manually
How about downlaoding and installing updates manually? At least it will give you some time to know whether an update is broken. Go to [Windows 10 update history](https://support.microsoft.com/en-in/help/4555932) page and select your Windows 10 version in the sidebar. Below there in the **In this release** section, click on an update. You will be presented with a page that tells about what the update fixes, what known issues it has along with its workaround (if possible) and where to download it from.

{{% center %}}
{{% figure src="/img/kb.png" title="A page with details on Windows updates" alt="A page with details on Windows updates" %}}
{{% /center %}}

Now copy the Knowledge Base number (which is KB4579311 in the screenshot). Go to [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx), paste the KB number and hit search. Now select the appropriate version and architecture and click on download. You will be presented with a new window with a download link. Click it and it should start downloading. After the download is complete, execute it (select **Yes** if prompted by UAC) and it should start installing.

### Using PowerShell
Great. Now we have OS updates installing at our convenience but still have some important ones missing. Windows Update delivers driver updates and updates for other (Microsoft) products which are not available in Microsoft Update Catalog. So we will use a PowerShell module called **PSWindowsUpdate**. Install it by entering the following line in PowerShell as Administrator.
```powershell
Install-Module PSWindowsUpdate
```

To get a list of commands, enter 
```powershell
Get-Command -Module PSWindowsUpdate
```

To know what a command does, enter
```powershell
Get-Help <cmdlet>
```

Right now, it is not configured to download updates for other Microsoft products. So we will enable it with the next step:
```powershell
Add-WUServiceManager -MicrosoftUpdate
```

Get a list of updates with
```powershell
Get-WUList -MicrosoftUpdate
```

The `MicrosoftUpdate` parameter tells `Get-WUList` to show updates for Microsoft products along with Windows updates.

{{% center %}}
{{% figure src="/img/get-wulist.png" title="Some updates" alt="Screenshot of the above command in action displaying a list of various updates" %}}
{{% /center %}}

As you can see, it pulls driver updates too so we got everything covered here. Next, we will install updates. There are several ways, use whichever you find the most convenient. The following command installs all available updates, reboots your PC and saves the log file in the specified location. Omit `AutoReboot` parameter if you want to reboot manually. Please reboot at your earliest convenience.
```powershell
Install-WindowsUpdate -AcceptAll -Install -AutoReboot | Out-File "c:\logs\$(Get-Date 0f yyyy-MM-dd)-WindowsUpdate.log" -force
```

Or you can install speciifc update packages by entering
```powershell
Get-WindowsUpdate -KBArticleID <KB number>, <also KB number but optional and so on...> -Install
```

Execlude update packages:
```powershell
Install-WindowsUpdate -NotCategory <category> -NotTitle <title> -NotKBArticleID <article ID> -AcceptAll -IgnoreReboot
```

The `IgnoreReboot` parameter does not reboot your PC automatically and `AcceptAll` parameter will not require you to approve installation of packages individually.

Get Windows Update history with
```powershell
Get-WUHistory
```

You can know more about PSWindowsUpdate [here](http://woshub.com/pswindowsupdate-module/).

## Time to "fix" speed
Alright. We fixed the issue of forced updates but that does not fix the issue of Windows 10 slowing down over time. Most of it has to do with what programs you have installed in your PC and the hardware constituting it. Still, that does not stop us from trying to fix it on OS level. It is more about computing habits than using a magic utility that runs your computer at the speed of light.

### Check which apps run at startup and disable them if necessary
I think I do not need to explain what happens when we disable unnecessary apps from running at startup. Go to **Settings > Apps > Startup** and toggle the switch for unnecessary apps to off.

### Some advice
Here I will list some advice to help keep your computer running at a decent speed. Again, most of it depends on the hardware, programs installed on your computer and your computing habits. I will not list the steps since there are plenty of tutorial avaialble for them.
- **Partition your storage and save all your stuff there.** This will quicken the installation of feature updates. Also, you will not lose your data if you ever need to reinstall Windows for whatever reason. 
- **Install portable version of programs as much as possible.** This way they get installed with normal provoleges and (mostly) do not run in background unless absoloutely needed. Keep them in the other partition. This way you will not lose them if you happen to reinstall Windows. 
- **Use a package manager to install programs.** Have them configured to install portable version, if possible. Also, this allows tou to manage the installed packages from command line in a convenient way.

## Time to say bye
I follow all of the steps mentioned above and have never had Windows break on me due to any of the given steps/advice. However, I cannot guarantee that the same will be true in your case too. It is entirely possible that you might render your computer unbootable by following the steps. So be careful and know what you are doing.

Thank you and stay safe.
