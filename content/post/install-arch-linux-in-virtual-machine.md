---
title: "Install Arch Linux in Virtual Machine (KVM/QEMU)"
date: 2022-04-22T06:52:27Z
lastmod: 2022-04-22T06:52:27Z
draft: false
keywords: [kvm qemu arch vm "virtual machine" libvirt]
description: ""
tags: [libvirt]
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

Installing Arch Linux on a KVM/QEMU virtual macin eis a fairly startightforward process. We will use command line approach in this post.

<!--more-->

## Create a virtual machine
Here we use `virt-install` to create a virtual machine. Run the following as root:
```bash
virt-install -n arch \
--os-type=linux \
--os-variant=archlinux \
--ram=2048 \
--vcpus=4 \
--disk path=/var/lib/libvirt/images/arch.qcow2,bus=virtio,size=32 \
--graphics=spice \
--cdrom /path/to/image.iso \
--network default
```

### What is happening here?
- Here we specify name for the virtual machine, which is *arch*.
- Next we specify type and variant.
- The amount of RAM is specified in megabytes.
- We assign four virtual CPUs to the VM.
- In `disk path` we have given the default location where the virtual machine image is stored.
- Size is in gigabyes.
- We have used *spice* for graphics. Alternatively, VNC can be used.
- In `cdrom` we have provided the location of the boot media.
- Finally we asked `virt-install` to use the default network profile.

Pressing Enter will start the creation of the virtual machine. When done, it will start *virt-viewer* with live media running.

## Install Arch Linux
This part is the same as any Arch Linux installation. We partition disks, run bootstrap script, generate locales, set timezone, hostname, install bootloader and optionally a desktop environment.

### Partition disks
The virtual storage goes by `/dev/sda`. We partition disks using `cfdisk`. Select **dos** partition tabke since we are not using UEFI. Create just one partition and use it as root. Write then quit the program.

Now we format the partition.
```bash
mkfs.ext4 /dev/vda1
```

### Mount root partition and install
Now we are ready to install Arch Linux. Mount root partition on `/mnt` with `mount /dev/vda1 /mnt`. Then we run the bootstrap script that will create the necessary files and install the specified packages.
```bash
pacstrap /mnt base base-devel linux vim dhcpcd
```

#### What is happening here?
- Here we asked `pacstrap` to install Arch Linux on `/mnt`.
- Then we specified the packages to be installed.
- The meta package `base` contains a minimal package set that defines a basic Arch Linux installation. It includes basic utilities like glibc, bash, coreutils, networking tools, systemd, etc. 
- Next is the `base-devel` group. It contains tools to build many packages like gcc, make, binutils, etc.
- `linux` is the kernel.
- We require `vim` to configure various things. Any text editor will do.
- `dhcpcd` will allow us to connect to the internet. Alternatively, `networkmanager` can be used.

### Generate fstab
This file tells which partitions to mount during boot. Instead of writing by hand, we use a tool to simplify things for us. Running the following
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
will generate a fstab file. We asked it to use `/mnt` partition. Any partition under `/mnt` will also be included, so if you created separate `/home` and `/var` partitions, they will also be included. The `-U` parameter tells `genfstab` to use UUID.

## Configuration
In this section we set timezones, locale and hostname.

Chroot into new root
```bash
arch-chroot /mnt
```

### Set timezone
List timezones by running `timedatectl list-timezones`. Set timezone with
```bash
timedatectl set-timezone Asia/Kolkata
```
Replace **Asia/Kolkata** with your timezone.

### Set locale
Uncomment the desired locales in `locale.gen`
```bash
vim /etc/locale.gen
```
Save and exit then generate them by running
```bash
locale-gen
```
And set locale config
```bash
echo LANG=en_US.UTF-8 > /etc/locale.conf
export LANG=en_US.UTF-8
```
Replace **en_US.UTF-8** with your locale.

### Configure network
#### Set hostname
Your virtual machine will be identified by this name on the network
```bash
echo arch-vm > /etc/hostname
```

#### Create /etc/hosts
Your computer will look here for resolving domain names before looking out on the internet. Put the following information in `/etc/hosts`:
```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch-vm
```

#### Enable dhcpcd
This will enable the virtual machine to connect to the internet
```bash
systemctl enable dhcpcd.service
```

### Create a user
Before we create a regular user, it becomes necessary to set a root password. Set it by running 
```bash
passwd 
```
Create a new user with
```bash
useradd -m -G wheel,storage,power -s /bin/bash <username>
```

#### What is happening here?
- `-m` tells `useradd` ro create a home directory for this user.
- `-G` tells which groups this user will belong to.
- `-s` specifies the login shell for this user.
- Replace &lt;username&gt; with a username of your choice.

Set a password for this user with
```bash
passwd <username>
```

### Install ssh server
If working on command line is all you have to do, then working via SSH is more convenient than using Spice client (or VNC if you chose that). Install it 
```bash
pacman -S openssh
```
and enable it 
```bash
systemctl enable sshd.service
```

### Install bootloader
The instructions thus far will provide us a working bare minimum Arch Linux environment. Now it is time to install a bootloadet and finish the installation. We will go with grub.
```bash
pacman -S grub
grub-install /dev/vda
grub-mkconfig -o /boot/grub/grub.cfg
```
#### What is happening here?
- First we install grub package.
- Then we install it on the device `/dev/vda`.
- Next we create a configuration file. It can be modified at `/etc/default/grub`. Every time we modify the configuration, it must be generated with the last command for the changes to take place.

### Reboot
The installation is complete. Next time we boot, we will be dropped into a working Ach Linux environment. 

Exit chroot.
```bash
exit
```
Unmount partition
```bash
umoount /mnt 
```
And reboot 
```bash
reboot
```

## Connecting to the virtual machine
Wait for a minute and the virtual machine will be up. FInd the virtual machine's IP address on host machine (assuming **arch** is the name of your virtual machine)
```bash
virsh domifaddr arch
```

Save the IP address in your host's `/etc/hosts`
```bash
printf "<ip-address>\t<guest hostname>\n" | sudo tee -a /etc/hosts
```
Replace `<ip-address>` with the IP address of the virtual machine and `<guest hostname>` with the hostname you set while installing the virtual machine. It wuold be something like
```bash
printf "192.168.122.43\tarch-vm\n" | sudo tee -a /etc/hosts
```

### Create SSH key pair
While not necessary on a local network, it is generally a good habit to use key pair instead of password authentication.

Go to `~/.ssh` and create a key pair.
```bash
cd ~/.ssh 
ssh-keygen -t ed25519 -f arch-vm -C "arch linux virtual machine"
```

#### What is happening here?
- `ssh-keygen` generates keys. 
- `-t` specifies the type of the key. We chose Ed25519. Other popular types are EdDSA, RSA, DSA, etc.
- `-f` specifies the filename of this key pair. 
- `-C` is used to put comment in the key.

This process will ask for a passphrase and generates a private key and a public key. The public key will have extension `.pub`. 

Create a config file. This will tell SSH which key to use with respect to host. The file is named `config`. Put the following in the file
```
Host arch-vm
  IdentityFile ~/.ssh/arch-vm
```

The private key is used to identify you. Anybody with your private key will be impersonate you so keep it safe.

Now copy the public key over to your virtual machine. It can be achieved by programs like `scp` or `rsync`. However, we will use `ssh-copy-id` to conveniently put our key while abstracting most parts. 
```bash
ssh-copy-id -f -i ~/.ssh/arch arch@arch-vm
```
It is assuming **arch** is the username of the normal user of the virtual machine. 

Now log in by running
```bash
ssh arch@arch-vm
```

Modify `/etc/ssh/sshd_config` to only use public key for authentication. 
- Change `PubkeyAuthentication` to `yes`
- Change `PasswordAuthentication` to `no`
- Change `X11Forwarding` to `yes`

Now restart `sshd` for changes to take place.
```bash
systemctl restart sshd.config
```
Exit and try logging in again. This time it should ask for passphrase.

## Install a desktop environment
This part is entirely optional if you wish to use GUI. In this post, we will install  MATE desktop environment. 
```bash
pacman -S mate mate-extra
```
The desktop environment can be started by running `exec mate-session`. If you want to use xinit then put the command in `~/.xinitrc`. Then install `xorg-xinit` package. Start it with `startx`.

 Alternatively you can use a display manager like GDM, LightDM, SDDM. etc. Install `sddm`, enable and start it 
 ```bash
 systemctl enable sddm.service
 systemctl start sddm.service
 ```

 If you wish to run GUI programs over X11 forwarding, a desktop environment might be overkill. In that case just install `xorg`, `xorg-server`. To use X11 forwarding, log in via ssh with
 ```bash
 ssh -Y arch@arch-vm
 ```
