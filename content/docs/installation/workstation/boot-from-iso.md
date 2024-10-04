---
bookToc: false
---
# Booting from ISO

The BurmillaOS ISO file can be used to create a fresh BurmillaOS install on KVM, VMware, VirtualBox, Hyper-V, Proxmox VE, or bare metal servers. You can download the `burmillaos.iso` file from our [releases page](https://github.com/burmilla/os/releases/).

Some hypervisors may require a built-in agent to communicate with the guest, for this, BurmillaOS precompiles some ISO files.

Hypervisor | ISO
--------   | ----------------
VMware     | burmillaos-vmware.iso
Hyper-V    | burmillaos-hyperv.iso
Proxmox VE | burmillaos-proxmoxve.iso

You must boot with enough memory which you can refer to [here](/#hardware-requirements). If you boot with the ISO, you will automatically be logged in as the `rancher` user. Only the ISO is set to use autologin by default. If you run from a cloud or install to disk, SSH keys or a password of your choice is expected to be used.

## Install to Disk

After you boot BurmillaOS from ISO, you can follow the instructions [here](/docs/installation/server/install-to-disk) to install BurmillaOS to a hard disk.
