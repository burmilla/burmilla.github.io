---
weight: 3
bookToc: true
---
# Upgrading to v3

BurmillaOS v3 is the next major version of BurmillaOS. It is currently in testing: the latest test version is [v3.0.0-rc1](https://github.com/burmilla/os/releases/tag/v3.0.0-rc1) (see the [releases page](/docs/releases)). v2.0.x stays the stable version until v3.0.0 is released.

Feedback from testers is welcome in the [v3.0 discussion](https://github.com/burmilla/os/discussions/226).

## What is new in v3

| Component              | v2.0.x (stable)                  | v3.0.0-rc1                                      |
|------------------------|----------------------------------|-------------------------------------------------|
| Kernel                 | 5.10.x                           | 6.12.114 (6.12.x LTS, with BTF support) |
| Buildroot (base)       | 2023.05                          | 2025.02.18                           |
| `os-base`              | v2023.05-1                       | v2025.02.18-1                             |
| `os-initrd-base`       | v2023.02.10-2                    | v2025.02.18-1                      |
| Default console        | Debian 11 "Bullseye"             | Debian 13 "Trixie"                              |
| System Docker          | 17.06.107                        | 17.06.113 (heavily stripped)                    |
| cgroups                | v1                               | v2 (unified hierarchy)                          |
| Boot / install         | BIOS (MBR + Syslinux)            | BIOS (MBR + Syslinux) and UEFI (GPT + Syslinux EFI) |
| Release channel        | `releases/v2.0.x`                | `releases/v3.0.x`                               |

In addition the `ros` binary has been heavily refactored: service definitions are now handled with [compose-go](https://github.com/compose-spec/compose-go) types instead of the unmaintained libcompose ones, and it is built with Go modules and Go 1.26. The default user Docker engine (`rancher.docker.engine`) is `latest`, so new installations switch to the latest engine available in the v3 service repository on first boot.

### Changes since beta1

- Kernel updated to 6.12.114 and Buildroot to the 2025.02.18 LTS release.
- AppArmor is enabled again; profiles (including Docker's `docker-default`) are loaded when the console starts.
- The Hyper-V KVP daemon no longer crash-loops.
- Enabling the ZFS service works again (OpenZFS 2.4.4, which supports kernel 6.12).
- All Docker engines offered on v2.0.x, plus newer ones, are available for v3. If the configured engine is missing from the service repository, BurmillaOS falls back to the default engine so Docker still starts.
- The console now includes `multipath-tools` for SAN multipath (`nfs-common` and `open-iscsi` were already included).

## Breaking changes

### cgroup v2 only

BurmillaOS v3 mounts the cgroup v2 unified hierarchy on `/sys/fs/cgroup`. The cgroup v1 hierarchies (including the `name=systemd` cgroup which the console used to mount) are no longer mounted, and there is no option to switch back to cgroup v1.

Software which requires cgroup v1 will not work on v3. For example:

- Docker engines older than 20.10 (cgroup v2 support was added in Docker 20.10). The engines offered by `ros engine list` on v3 are all recent versions.
- Old Kubernetes / K3s versions and container images which run `systemd` or other tools that expect a cgroup v1 layout.
- Monitoring tools which read cgroup v1 paths such as `/sys/fs/cgroup/memory/...` directly.

Check that your workloads support cgroup v2 before upgrading.

### New default console: Debian 13 "Trixie"

The `default` console is now based on `debian:trixie-slim`. Packages and package names follow Debian 13, for example `lastlog` is provided by `lastlog2`.

The console container is persistent, so `ros os upgrade` does **not** replace an existing console. After upgrading from v2 you keep running the old Debian 11 based console until you switch it, see [Upgrading from v2](#upgrading-from-v2).

### UEFI installation

The ISO now boots in both BIOS and UEFI mode (UEFI on x86_64 only). `ros install` selects the boot mode automatically based on how the installer was booted:

- **BIOS boot:** the disk gets an MBR partition table with a single ext4 `RANCHER_STATE` partition and the Syslinux (extlinux) boot loader, as in v2.
- **UEFI boot:** the disk gets a GPT partition table with a 512 MiB FAT32 EFI system partition labeled `RANCHER_EFI` (holding Syslinux EFI, the kernel, the initrd and the boot configs) and an ext4 `RANCHER_STATE` partition. The EFI system partition is mounted on `/boot` on the installed system.

When booted in UEFI mode a whole disk is needed: `ros install --partition` can not be used together with the `generic`/`syslinux` install types. The ISO uses GRUB as the UEFI boot loader for the ISO/USB media only; installed systems always use Syslinux. The boot loaders are not signed, so Secure Boot must be disabled.

Upgrades keep whatever boot layout the installed system already uses: a v2 (BIOS) installation stays a BIOS installation after upgrading to v3.

### Removed install types

The following `ros install --install-type` values were removed:

- `gptsyslinux` - use the default `generic` install type on a system booted in UEFI mode to get a GPT layout.
- `amazon-ebs-pv` - paravirtualized (PV-GRUB) Amazon instances are not supported anymore. GRUB support was removed from the installer.

The other install types (`generic`, `syslinux`, `amazon-ebs-hvm`, `googlecompute`, `noformat`, `raid`, `bootstrap`, `upgrade`) still exist. No other `ros` commands or options were removed, see the [reference](/docs/reference).

### Stripped System Docker

System Docker has been stripped down to what BurmillaOS needs to run its own system services. Use the user Docker (`docker`) for your own workloads and keep the use of `system-docker` to [system services](/docs/system-services/).

The service definitions in cloud-config are now parsed by compose-go. Existing service definitions are intended to keep working, but clearly invalid ones (for example an empty volume or a malformed port definition) are now reported and the service is skipped. Check `sudo ros service list` after upgrading if you use custom system services.

## Upgrading from v2

> **Caution:** v3 is still a test version. Test the upgrade on a non-critical system and have a backup of your data before upgrading.

A v2 system reads its release list from the v2 channel (`https://raw.githubusercontent.com/burmilla/releases/v2.0.x/releases.yml`), which does not contain v3 versions, so `ros os list` and a plain `ros os upgrade` do not offer v3. Upgrade by naming the image explicitly:

```bash
$ sudo ros os upgrade -i burmilla/os:v3.0.0-rc1
```

The v3 default configuration points to the v3 release channel (`https://raw.githubusercontent.com/burmilla/releases/v3.0.x/releases.yml`) and the v3 service repository, so after the upgrade `ros os list` shows v3 versions. If you have set `rancher.upgrade.url` yourself (for example when [migrating from RancherOS](/docs/installation/upgrading/#upgrade-rancheros-to-burmillaos)), point it to the v3 channel:

```bash
$ sudo ros config set rancher.upgrade.url \
https://raw.githubusercontent.com/burmilla/releases/v3.0.x/releases.yml
```

After rebooting into v3:

1. Switch to the new Debian 13 console. This is a destructive operation for anything installed into the console container, see [console persistence](/docs/installation/custom-builds/custom-console/#console-persistence).
   ```bash
   $ sudo ros console switch default
   ```
   Alternatively use `sudo ros os upgrade -i burmilla/os:v3.0.0-rc1 --upgrade-console` to replace the console as part of the upgrade.
2. Check the user Docker engine. The v3 service repository offers all engines that v2.0.x offers, plus newer ones. If the configured engine is still not available, BurmillaOS falls back to the default engine. To pick a specific engine, list the available engines and switch to one of them:
   ```bash
   $ sudo ros engine list
   $ sudo ros engine switch <engine from the list>
   ```
3. Verify that your workloads work with cgroup v2.

### Rolling back to v2

The previous kernel and initrd are kept, so the previous version can be selected from the Syslinux boot menu. Rolling back with `sudo ros os upgrade -i burmilla/os:v2.0.6` is not tested for v3 to v2 rollbacks:

- The v2 installer does not know the UEFI layout, so do not roll back a system which was installed with v3 in UEFI mode.
- Rolling back while running the newer persistent console is not supported. If you already switched to the Debian 13 console, run `sudo ros console switch default` after the rollback (see the note about persistent consoles in [Upgrading](/docs/installation/upgrading/#rolling-back-an-upgrade)).

## Known issues

- AppArmor is not enabled in the arm64 kernel.
- On x86_64, AppArmor depends on the `security=apparmor` kernel parameter being set.
- The Hyper-V VSS and fcopy daemons are included but not started; only the KVP daemon runs.
- When `rancher.docker.engine` is `latest`, engine versions are compared as text, so for example `docker-29.9.x` is picked over `docker-29.10.x`.

New reports are welcome in the [v3.0 discussion](https://github.com/burmilla/os/discussions/226).
