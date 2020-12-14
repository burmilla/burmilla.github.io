---
weight: 2
---
# Upgrading

If BurmillaOS has released a new version and you want to learn how to upgrade your OS, we make it easy using the `ros os` command.

Since BurmillaOS is a kernel and initrd, the upgrade process is downloading a new kernel and initrd, and updating the boot loader to point to it. The old kernel and initrd are not removed. If there is a problem with your upgrade, you can select the old kernel from the Syslinux bootloader.

Before upgrading to any version, please review the release notes on our [releases page](https://github.com/burmilla/os/releases) in GitHub to review any updates in the release.

> **Note:** If you are using [`docker-machine`](/docs/installation/workstation/docker-machine) then you will not be able to upgrade your BurmillaOS version. You need to delete and re-create the machine.


## Upgrade RancherOS to BurmillaOS

Permanently upgrade your existing RancherOS installation to BurmillaOS and begin tracking BurmillaOS releases:

```bash
$ sudo ros console switch default
$ sudo ros config set rancher.upgrade.url \
https://raw.githubusercontent.com/burmilla/releases/v1.9.x/releases.yml
$ sudo ros os upgrade
```

## Version Control

First, let's check what version you have running on your system.

```bash
$ sudo ros os version
v1.9.0
```

If you just want to find out the available releases from the command line, it's a simple command.

```bash
# List all available releases
$ sudo ros os list
burmilla/os:v1.9.0 local latest running
burmilla/os:v1.9.0-rc1 remote available
```

The `local`/`remote` label shows which images are available to System Docker locally versus which need to be pulled from Docker Hub. If you choose to upgrade to a version that is remote, we will automatically pull that image during the upgrade.

## Upgrading

Let's walk through upgrading! The `ros os upgrade` command will automatically upgrade to the current release of BurmillaOS. The current release is designated as the most recent release of BurmillaOS.

```bash
$ sudo ros os upgrade
Upgrading to burmilla/os:v1.9.0
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

After rebooting, you can check that your version has been updated.

```bash
$ sudo ros -v
ros version v0.5.0
```

> **Note:** If you are booting from ISO and have not installed to disk, your upgrade will not be saved. You can view our guide to [installing to disk](/docs/installation/server/install-to-disk).

#### Upgrading to a Specific Version

If you are a couple of versions behind the current version, use the `-i` option to pick the version that you want to upgrade to.

```bash
$ sudo ros os upgrade -i burmilla/os:v0.5.0
Upgrading to burmilla/os:v0.5.0
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
ros version v0.4.5
$ sudo ros os upgrade -i burmilla/os:v0.4.4
Upgrading to burmilla/os:v0.4.4
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
ros version 0.4.4
```

<br>

> **Note:** If you are using a [persistent console](/docs/installation/custom-builds/custom-console#console-persistence) and in the current version's console, rolling back is not supported. For example, rolling back to v0.4.5 when using a v0.5.0 persistent console is not supported.

### Staging an Upgrade

During an upgrade, the template of the upgrade is downloaded from the burmilla/os repository. You can download this template ahead of time so that it's saved locally. This will decrease the time it takes to upgrade. We'll use the `-s` option to stage the specific template. You will need to specify the image name with the `-i` option, otherwise it will automatically stage the current version.

```bash
$ sudo ros os upgrade -s -i burmilla/os:v1.9.0
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
