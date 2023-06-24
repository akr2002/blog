---
title: "Enable Bluetooth in Nixos"
date: 2023-06-24T10:15:14+05:30
lastmod: 2023-06-24T10:15:14+05:30
draft: false
keywords: [bluetooth nixos]
description: ""
tags: [nixos]
categories: [linux]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
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
NixOS is a Linux distribution that is built on the Nix package manager. It allows you to declaratively specify your system configuration in a file called /etc/nixos/configuration.nix and then apply it with a single command. This makes NixOS very reproducible, reliable and customizable.

One of the features that you might want to enable on your NixOS system is Bluetooth support. Bluetooth is a wireless technology that allows you to connect various devices such as keyboards, mice, headphones, speakers and more. In this blog post, I will show you how to enable Bluetooth support on NixOS and how to pair and connect your Bluetooth devices.

<!--more-->

## Enabling Bluetooth Support

To enable Bluetooth support on NixOS, you need to add the following line to your /etc/nixos/configuration.nix file:

```nix
hardware.bluetooth.enable = true;
```

This will enable the hardware.bluetooth module that provides the necessary drivers and services for Bluetooth. You can also customize some options such as the device name, the pin code and the power management settings. For more details, you can check the [NixOS manual](https://nixos.org/manual/nixos/stable/index.html#sec-hardware-bluetooth).

If you want to check the battery status of your Bluetooth devices, you need to enable the experimental feature of BlueZ by adding the following snippet to your configuration.nix file:

```nix
hardware.bluetooth = {
  enable = true;
  settings = {
    General = {
      Experimental = "true";
    };
  };
};
```
This will allow you to use the bluetooth-battery python script or the GNOME extension to see the battery level of your devices.

After adding the lines to your configuration.nix file, you need to run the following command as root to apply the changes:

```bash
nixos-rebuild switch
```

This will rebuild your system configuration and activate the new settings. You can also use `nixos-rebuild test` or `nixos-rebuild boot` if you want to test or boot into the new configuration without switching to it immediately.

### Checking Bluetooth Device Battery Status

## Pairing Bluetooth Devices

After enabling Bluetooth support on NixOS, you need to pair your Bluetooth devices with your NixOS machine. There are two ways to do this: using a graphical user interface (GUI) or using a command line tool.

### Using a GUI

If you are using a desktop environment that provides a Bluetooth management GUI, such as GNOME, KDE or Cinnamon, you can use it to pair your devices. You can usually find the Bluetooth icon in the system tray or the settings menu. You can click on it and then select "Add Device" or "Set Up New Device" to start the pairing process. You will see a list of available devices and you can select the one you want to pair with. You might need to enter a pin code or confirm the pairing on both devices.

If your desktop environment does not provide a Bluetooth management GUI, you can additionally enable the blueman service in your configuration.nix file with the following line:

```nix
services.blueman.enable = true;
```

This will install and enable blueman, a GTK-based Bluetooth manager that provides blueman-applet and blueman-manager. You can run blueman-applet to show the Bluetooth icon in the system tray and blueman-manager to open a window where you can manage your devices. You can use them in a similar way as described above.

### Using bluetoothctl

Alternatively, you can pair your devices from the command line using bluetoothctl, a tool that is part of the BlueZ package. BlueZ is the official Linux Bluetooth protocol stack and it provides various utilities and libraries for Bluetooth functionality.

To use bluetoothctl, you need to open a terminal and run it with:

```bash
bluetoothctl
```

This will start an interactive shell where you can enter various commands to control your Bluetooth devices. You will see something like this:

```bash
Agent registered
[bluetooth]#
```

The `[bluetooth]#` prompt indicates that you are in bluetoothctl mode. You can type `help` to see a list of available commands and their descriptions. Some of the most useful commands are:

- `power on`: Turns on the Bluetooth controller.
- `agent on`: Enables the default agent that handles pairing requests.
- `default-agent`: Sets the default agent as the default one.
- `scan on`: Starts scanning for nearby devices.
- `pair [hex-address]`: Pairs with the device with the given hexadecimal address.
- `connect [hex-address]`: Connects to the device with the given hexadecimal address.
- `trust [hex-address]`: Trusts the device with the given hexadecimal address so that it automatically connects when available.
- `info [hex-address]`: Shows information about the device with the given hexadecimal address.
- `devices`: Shows a list of paired devices.
- `remove [hex-address]`: Removes the device with the given hexadecimal address from the list of paired devices.
- `quit`: Exits bluetoothctl mode.

To pair a device with bluetoothctl, you need to follow these steps:

1. Turn on the Bluetooth controller with `power on`.
2. Enable the default agent with `agent on` and `default-agent`.
3. Start scanning for nearby devices with `scan on`.
4. Put your device in pairing mode and wait for its hexadecimal address to appear in the output of bluetoothctl.
5. Pair with the device with `pair [hex-address]` where you replace [hex-address] with the actual address of your device.
6. Connect to the device with `connect [hex-address]`.
7. Trust the device with `trust [hex-address]` if you want it to automatically connect when available.

You can also use tab completion to enter the hexadecimal address of your device more easily. For example, if your device's address is 11:22:33:44:55:66, you can type `pair 11` and then press tab to complete the rest of the address.

## Using Bluetooth Devices

After pairing and connecting your Bluetooth devices, you can use them as normal. For example, if you paired a Bluetooth keyboard or mouse, you can use them to input commands or move the cursor. If you paired a Bluetooth headset or speaker, you can use them to listen to audio or make calls.

If you are using a Bluetooth headset with PulseAudio, you will need to enable both hardware.pulseaudio.enable and hardware.bluetooth.enable in your configuration.nix file as follows:

```nix
hardware.pulseaudio.enable = true;
hardware.bluetooth.enable = true;
```

This will allow Bluetooth audio devices to be used with PulseAudio, the sound server that handles audio playback and recording on NixOS. You will also need to restart PulseAudio with:

```bash
systemctl --user daemon-reload; systemctl --user restart pulseaudio
```

You can verify that PulseAudio has loaded the Bluetooth module by running:

```bash
pactl list | grep -i 'Name.*module.*blue'
```

Bluetooth modules should be present in the list. You can also use tools like pavucontrol or pactl to manage your audio devices and streams.

If your Bluetooth headset has buttons for pause/play or to skip to the next track, you can use them to control media players that support the dbus-based MPRIS standard, such as VLC, Spotify or Rhythmbox. To make this work, you need to use mpris-proxy that is part of the BlueZ package. You can enable it as a systemd user service in your configuration.nix file with the following snippet:

```nix
systemd.user.services.mpris-proxy = {
  Unit.Description = "Mpris proxy";
  Unit.After = [ "network.target" "sound.target" ];
  Service.ExecStart = "${pkgs.bluez}/bin/mpris-proxy";
  Install.WantedBy = [ "default.target" ];
};
```

This will start mpris-proxy as a daemon that translates Bluetooth headset button events into MPRIS commands for media players.

## Conclusion

In this blog post, I showed you how to enable Bluetooth support on NixOS and how to pair and connect your Bluetooth devices. I also showed you how to use Bluetooth headsets with PulseAudio and how to control media players with Bluetooth headset buttons. I hope you found this post useful and informative.
