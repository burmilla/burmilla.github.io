---
title: Kernel boot parameters
weight: 1
---
# Kernel boot parameters

BurmillaOS parses the Linux kernel boot cmdline to add any keys it understands to its configuration. This allows you to modify what cloud-init sources it will use on boot, to enable `rancher.debug` logging, or to almost any other configuration setting.

There are two ways to set or modify persistent kernel parameters, in-place (editing the file and reboot) or during installation to disk.

## In-place editing

_Available as of RancherOS v1.1_

To edit the kernel boot parameters of an already installed BurmillaOS system, use the new `sudo ros config syslinux` editing command (uses `vi`).

> To activate this setting, you will need to reboot.

## During installation

If you want to set the extra kernel parameters when you are [Installing BurmillaOS to Disk](/docs/installation/server/install-to-disk) please use the `--append` parameter.

```bash
$ sudo ros install -d /dev/sda --append "rancher.autologin=tty1"
```

## Graphical boot screen

_Available as of RancherOS v1.1_

RancherOS v1.1 added a Syslinux boot menu, which allows you to temporarily edit the boot parameters, or to select "Debug logging", "Autologin", both "Debug logging & Autologin" and "Recovery Console".

On desktop systems the Syslinux boot menu can be switched to graphical mode by adding `UI vesamenu.c32` to a new line in `global.cfg` (use `sudo ros config syslinux` to edit the file).

## Useful BurmillaOS kernel boot parameters

### User password

`rancher.password=<passwd...>` will set the password for the `rancher` user. If you are not willing to use SSH keys, you can consider this parameter.

### Recovery console

`rancher.recovery=true` will start a single user `root` bash session as easily in the boot process, with no network, or persistent filesystem mounted. This can be used to fix disk problems, or to debug your system.

### Enable/Disable sshd

`rancher.ssh.daemon=false` (its enabled in the os-config) can be used to start your BurmillaOS with no sshd daemon. This can be used to further reduce the ports that your system is listening on.

### Enable debug logging

`rancher.debug=true` will log everything to the console for debugging.

### Autologin console

`rancher.autologin=<tty...>` will automatically log in the specified console - common values are `tty1`, `ttyS0` and `ttyAMA0` - depending on your platform.

### Enable/Disable hypervisor service auto-enable

_Available as of RancherOS v1.3_

RancherOS v1.3 added detection of Hypervisor, and then will try to download the a service called `<hypervisor>-vm-tools`. This may cause boot speed issues, and so can be disabled by setting `rancher.hypervisor_service=false`.

### Auto reboot after a kernel panic

_Available as of RancherOS v1.3_

`panic=10` will automatically reboot after a kernel panic, 10 means wait 10 seconds before reboot. This is a common kernel parameter, pointing out that it is because we set this parameter by default.
