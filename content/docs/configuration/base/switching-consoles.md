---
title: Consoles
weight: 1
bookToc: false
---
# Consoles

When [booting from the ISO](/docs/installation/workstation/boot-from-iso), BurmillaOS starts with the default console, which is based on Debian.

You can select which console you want BurmillaOS to start with using the [cloud-config](/docs/configuration/#cloud-config).

## Enabling Consoles using Cloud-Config

When launching BurmillaOS with a [cloud-config](/docs/configuration/#cloud-config) file, you can select which console you want to use.

Currently, the list of available consoles are:

* default (debian)

Here is an example cloud-config file that can be used to enable the debian console.

```yaml
#cloud-config
rancher:
  console: debian
```

### Listing Available Consoles

You can easily list the available consoles in BurmillaOS and what their status is with `sudo ros console list`.

```shell
$ sudo ros console list
current  default
```

### Disabling console access

In most environments the console is only used to log in to the server, over SSH or on a tty. Servers which are managed in other ways (Docker API over TLS, a GitOps agent, [BurFleet](https://github.com/burmilla/os-fleet-manager)) can turn that off:

```shell
$ sudo ros console access disable
disabled
$ sudo ros console access status
disabled
```

This stops sshd and the logins on the ttys (including `rancher.autologin`) and sets `rancher.console_access: false`, so they stay off after a reboot. Established SSH sessions are kept, tty sessions are hung up. The console container itself keeps running, because User Docker runs inside it and uses its tools, so Docker volumes on iSCSI or NFS keep working. `sudo ros console access enable` (or any other way to run `ros`, e.g. a privileged container with `/var/run/system-docker.sock`) starts sshd and the tty logins again, without a reboot and without restarting Docker.

The same can be set in the cloud-config:

```yaml
#cloud-config
rancher:
  console_access: false
```

BurFleet manages this for a whole fleet with the `console_access` setting.

### Console persistence

The default console is persistent. Persistent console means that the console container will remain the same and preserves changes made to its filesystem across reboots. If a container is deleted/rebuilt, state in the console will be lost except what is in the persisted directories.

```
/home
/opt
/var/lib/docker
/var/lib/rancher
```

<br>

> **Note:** When using a persistent console and in the current version's console, [rolling back](/docs/installation/upgrading#rolling-back-an-upgrade) is not supported. For example, rolling back to v0.4.5 when using a v0.5.0 persistent console is not supported.