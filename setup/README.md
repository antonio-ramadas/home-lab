# Setup

## Operating System

There are many options. I've no particular endorsement for a specific OS. Raspberry Pi OS seems like a great start. Its UI is nice and it should be easy to look up any issues that may come up. I recommend choosing the one with GUI as it is easier for non-technical people to use it.

## User

Follow the [guide from Raspberry Pi](https://www.raspberrypi.org/documentation/linux/usage/users.md) to change default password.
By default, `pi` user does not require password input for `sudo` commands. This is dangerous. Comment the contents of `/etc/sudoers.d/010_pi-nopasswd` with `visudo`.

## Remote Access

Raspberry Pi provides a [neat guide](https://www.raspberrypi.org/documentation/remote-access/). Follow the steps for VNC and SSH. Sections below are to be made after these two have been enabled.

Default resolution is set to something like 640x480. [Another useful guide](https://www.raspberrypi.org/documentation/configuration/config-txt/video.md) details the several available video options. Increasing it to 720p is as easy as to add the following to `/boot/config.txt`:

```bash
hdmi_ignore_edid=0xa5000080
hdmi_group=2
hdmi_mode=85
```

### SSH

By default SSH will keep on asking for the user's password. So, let's automate that with Public Key Authentication:

```bash
# Generate a new key pair
ssh-keygen -t rsa -b 4096 -C "some comment to identify the key" -f ~/.ssh/<FILENAME>

# Copy the public key to the device
ssh-copy-id -i ~/.ssh/<FILENAME>.pub <USERNAME>@<IP>

# Login with no password
ssh -i ~/.ssh/<FILENAME> <USERNAME>@<IP>
```

Instead of memorizing the IP, it is possible to use `raspberrypi.local` (mDNS). This can be changed in `/etc/hostname`.

We're not going to disable access with password as I want to have the flexibility to access from anywhere. The downsides are that I'll be more prone to attacks, and I'll have to enter a lengthy password.

### SSHFS

This is an optional step. Only to be made if needed to mount the device on the filesystem.

Follow the [official guide](https://www.raspberrypi.org/documentation/remote-access/ssh/sshfs.md). Use with `-o auto_cache` to prevent cache problems (directory not up-to-date).

If it gives an error when using `sshfs`, then it is also necessary to install [FUSE for OS X](https://osxfuse.github.io/).

Unmounting from the directory can be made with `umount`.

When seeded, follow [these steps to uninstall SSHFS](https://github.com/osxfuse/osxfuse/wiki/SSHFS#uninstallation):

```bash
sudo rm /usr/local/bin/sshfs
sudo rm /usr/local/share/man/man1/sshfs.1
sudo pkgutil --forget com.github.osxfuse.pkg.SSHFS
```

Uninstaller for FUSE for OS X is coupled with the installation files.

## Security

Don't blindly rely on defaults. Change them as the ultimate goal is to expose to the internet.

### Firewall

Before opening to the World Wide Web, it is important to restrict unexpected accesses. We're following the [guide from Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/security.md). It advices to go with [`ufw`](https://launchpad.net/ufw). It is very simple, but now exactly the most versatile. That's a tradeoff I'm willing to make for now. I'll revisit this if necessary.

```bash
# Install Uncomplicated Firewall (ufw)
sudo apt install ufw

# Limit SSH (the config limit is hard coded to deny IPs that do more than 6 connections in the last 30 seconds)
sudo ufw limit ssh

# Same for VNC
sudo ufw limit vnc

# And for rsync
sudo ufw limit rsync comment rsync

# Good to go!
sudo ufw enable
```

### Banning

https://serverfault.com/questions/90725/are-ip-addresses-trivial-to-forge

Again, Raspberry Pi goes to extra lengths to help out. [Fail2ban](https://www.fail2ban.org/) will ban IPs from all ports that are attempting to break in.

```bash
# Install Fail2ban
sudo apt install fail2ban

# Create file for local configurations
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Add the following to `/etc/fail2ban/jail.local` just before `[sshd]`:

```bash
[ssh]

# This is a custom entry following the guide from Raspberry Pi
# https://www.raspberrypi.org/documentation/configuration/security.md
enabled         = true
port            = ssh
filter          = sshd
logpath         = %(sshd_log)s
maxretry        = 6
```

### Useful resources

- [Raspberry Pi security guide](https://www.raspberrypi.org/documentation/configuration/security.md)

## Handling external devices

[Raspberry Pi guide](https://www.raspberrypi.org/documentation/configuration/external-storage.md) is very complete. Should be at least enough to start with.

## Boot from SD card and operate on external storage

SD card should only be used when starting up. Do not rely on it for writing frequently or for storing data on long term. SD card corruption is a serious problem.
The device is expected to be running 24/7. So, interaction with the SD card should rarely occur.

The goal here is to move the root partition to external storage, but have the boot partition on the SD card (writes to that partition will rarely happen (e.g., update of the kernel)). There should still be a root partition on the SD card, but it won't be enabled. Instead, it can act as a redundant copy and enable using the Raspberry Pi without anything attached (I'll just have to tweak a config). It won't have the same capacity, but at least will have all the useful stuff (i.e., ssh, vnc, firewall, ...). We just need to keep in mind that any significant changes should be carried out to the SD card as well (e.g., changing the user password so we don't have to keep track of the old and new).

Luckily, Pragmatic Linux has provided an [excellent guide on how to achieve this](https://www.pragmaticlinux.com/2020/08/move-the-raspberry-pi-root-file-system-to-a-usb-drive/). It's so good that there is a [local copy on the repository](articles/move-the-raspberry-pi-root-file-system-to-a-usb-drive.md) if it ever is taken down.

My current setup is with a Raspberry Pi 3B and I don't want to make any [permanent changes to boot from external devices without the need for a SD card](https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/msd.md).

## Logging to RAM

Logging places an heavy write load on the SD card which helps shorten its life. One way to spare it is to mount the log directory to RAM and flush the contents every once in a while.

[log2ram](https://github.com/azlux/log2ram) does the trick. Small and efficient that does not get in the way. Install and uninstall instructions are on the README file of the repository. Be sure it ran successfully after rebooting (`systemctl status log2ram`).

## Adding new software

Avoid installing applications without containers. This project is educational and mistakes will be made. Having the ability to fully remove what we installed is a great benefit and confidence booster. Sure, performance may be lost, but there is another advantage: deploying applications on a cluster. The recommended approach is to use containers due to their low overhead.

## Useful resources

- [Raspberry Pi configuration docs](https://www.raspberrypi.org/documentation/configuration/)
