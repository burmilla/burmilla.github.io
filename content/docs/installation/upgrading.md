---
weight: 2
---
# Upgrading

If BurmillaOS has released a new version and you want to learn how to upgrade your OS, we make it easy using the `ros os` command.

Since BurmillaOS is a kernel and initrd, the upgrade process is downloading a new kernel and initrd, and updating the boot loader to point to it. The old kernel and initrd are not removed. If there is a problem with your upgrade, you can select the old kernel from the Syslinux bootloader.

Before upgrading to any version, please review the release notes on our [releases page](https://github.com/burmilla/os/releases) in GitHub to review any updates in the release.

> **Note:** Upgrading from v2.0.x to v3.0.x contains breaking changes (e.g. cgroup v2 and a new console). Read [Upgrading to v3](/docs/installation/upgrading-to-v3) first.

> **Note:** If you are using [`docker-machine`](/docs/installation/workstation/docker-machine) then you will not be able to upgrade your BurmillaOS version. You need to delete and re-create the machine.


## Upgrade RancherOS to BurmillaOS

Permanently upgrade your existing RancherOS installation to BurmillaOS and begin tracking BurmillaOS releases:

```bash
$ sudo ros config set rancher.upgrade.url \
https://raw.githubusercontent.com/burmilla/releases/v2.0.x/releases.yml
$ sudo ros os upgrade
```
After reboot run also:
```bash
$ sudo ros console switch default
```
other why you are still using console from RancherOS (visible in `sudo system-docker ps` output)

## Version Control

First, let's check what version you have running on your system.

```bash
$ sudo ros os version
v2.0.0
```

If you just want to find out the available releases from the command line, it's a simple command.

```bash
# List all available releases
$ sudo ros os list
burmilla/os:v2.0.0 local latest running
burmilla/os:v2.0.0 remote available
```

The `local`/`remote` label shows which images are available to System Docker locally versus which need to be pulled from Docker Hub. If you choose to upgrade to a version that is remote, we will automatically pull that image during the upgrade.

## Upgrading

Let's walk through upgrading! The `ros os upgrade` command will automatically upgrade to the current release of BurmillaOS. The current release is designated as the most recent release of BurmillaOS.

```bash
$ sudo ros os upgrade
Upgrading to burmilla/os:v2.0.0
```

Confirm that you want to continue and the final step will be to confirm that you want to reboot.

```bash
Continue [y/N]: y
...
...
...
Continue with reboot [y/N]: y
INFO[0037] Rebooting
```

> **Note:** The default console container is persistent and will NOT be updated by default.  Use `ros console switch default` after the reboot to update the `os-console` container, or add `--upgrade-console` to `ros os upgrade` to replace the console as part of the upgrade.  Both are destructive operations for anything installed into the console container - see [console persistence](/docs/installation/custom-builds/custom-console/#console-persistence) for info about what will be retained.

> **Note:** To make the default container non-persistent, set `io.docker.compose.rebuild: "always"` on the console container [os-config.tpl](https://github.com/burmilla/os/blob/v2.0.0/os-config.tpl.yml#L246)

After rebooting, you can check that your version has been updated.

```bash
$ sudo ros -v
version v2.0.0 from os image burmilla/os:v2.0.0
```

> **Note:** If you are booting from ISO and have not installed to disk, your upgrade will not be saved. You can view our guide to [installing to disk](/docs/installation/server/install-to-disk).

#### Upgrading to a Specific Version

If you are a couple of versions behind the current version, use the `-i` option to pick the version that you want to upgrade to.

```bash
$ sudo ros os upgrade -i burmilla/os:v2.0.0
Upgrading to burmilla/os:v2.0.0
Continue [y/N]: y
...
...
...
Continue with reboot [y/N]: y
INFO[0082] Rebooting
```

#### Bypassing The Prompts

We have added the ability to bypass the prompts. Use the `-f` or `--force` option when upgrading. Your machine will automatically be rebooted and you'll just need to log back in when it's done.

If you want to bypass the prompts, but you don't want to immediately reboot, you can add `--no-reboot` to avoid rebooting immediately.

### Rolling back an Upgrade

If you've upgraded your BurmillaOS and something's not working anymore, you can easily rollback your upgrade.

The `ros os upgrade` command works for rolling back. We'll use the `-i` option to "upgrade" to a specific version. All you need to do is pick the previous version! Same as before, you will be prompted to confirm your upgrade version as well as confirm your reboot.

```bash
$ sudo ros -v
ros version v1.9.0
$ sudo ros os upgrade -i burmilla/os:v2.0.0
Upgrading to burmilla/os:v2.0.0
Continue [y/N]: y
...
...
...
Continue with reboot [y/N]: y
INFO[0082] Rebooting
```
After rebooting, the rollback will be complete.

```bash
$ sudo ros -v
version v2.0.0 from os image burmilla/os:v2.0.0
```

<br>

> **Note:** If you are using a [persistent console](/docs/installation/custom-builds/custom-console#console-persistence) and in the current version's console, rolling back is not supported. For example, rolling back to v0.4.5 when using a v0.5.0 persistent console is not supported.

### Staging an Upgrade

During an upgrade, the template of the upgrade is downloaded from the burmilla/os repository. You can download this template ahead of time so that it's saved locally. This will decrease the time it takes to upgrade. We'll use the `-s` option to stage the specific template. You will need to specify the image name with the `-i` option, otherwise it will automatically stage the current version.

```bash
$ sudo ros os upgrade -s -i burmilla/os:v2.0.0
```

### Cleaning Up After Upgrades

BurmillaOS does not remove anything when it is upgraded. Over the years a system which has been upgraded many times collects:

- Old `burmilla/os` (and `rancher/os`) installer images and old service images in System Docker.
- Old kernels and initrds in the boot directory of the state partition. Only the versions referenced by `linux-current.cfg` and `linux-previous.cfg` can be selected from the boot menu, the rest are unused.
- `usr-<version>` directories in the root of the state partition, one for every version that has been booted.

To remove unused images from System Docker (images which are still in use are kept, Docker prints an error for those which you can ignore):

```bash
$ sudo system-docker image prune -a
```

If your System Docker version does not have `image prune`, `sudo system-docker rmi $(sudo system-docker images -q)` does the same.

For the user Docker, `docker system prune` removes stopped containers, unused networks and dangling images as usual.

The boot files are not visible in the console. Mount the state partition to look at them (on a system installed in UEFI mode with v3.0.x, the kernels are on the EFI system partition labeled `RANCHER_EFI` instead):

```bash
$ sudo mkdir -p /mnt/state
$ sudo mount LABEL=RANCHER_STATE /mnt/state
$ ls -lh /mnt/state/boot
$ cat /mnt/state/boot/linux-current.cfg /mnt/state/boot/linux-previous.cfg
```

Every `vmlinuz-*` and `initrd-*` file which is **not** named in `linux-current.cfg` or `linux-previous.cfg` can be deleted, as can `usr-<version>` directories in `/mnt/state` whose version is neither the running nor the previous one. Do not touch `global.cfg`, `syslinux/`, `EFI/` or the files named in the two cfg files. Unmount the partition afterwards:

```bash
$ sudo umount /mnt/state
```

### Custom Upgrade Sources

In the `upgrade` key, the `url` is used to find the list of available and current versions of BurmillaOS. This can be modified to track custom builds and releases.

```yaml
#cloud-config
rancher:
  upgrade:
    url: https://raw.githubusercontent.com/burmilla/releases/master/releases.yml
    image: burmilla/os
```
