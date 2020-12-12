---
title: Consoles
weight: 1
bookToc: false
---
# Consoles

When [booting from the ISO](/docs/installation/workstation/boot-from-iso), BurmillaOS starts with the default console, which is based on busybox.

You can select which console you want BurmillaOS to start with using the [cloud-config](/docs/configuration/#cloud-config).

## Enabling Consoles using Cloud-Config

When launching BurmillaOS with a [cloud-config](/docs/configuration/#cloud-config) file, you can select which console you want to use.

Currently, the list of available consoles are:

* default (debian)

Here is an example cloud-config file that can be used to enable the debian console.

```yaml
#cloud-config
burmilla:
  console: debian
```

### Listing Available Consoles

You can easily list the available consoles in BurmillaOS and what their status is with `sudo ros console list`.

```shell
$ sudo ros console list
current  default
```

### Console persistence

The default console is persistent. Persistent console means that the console container will remain the same and preserves changes made to its filesystem across reboots. If a container is deleted/rebuilt, state in the console will be lost except what is in the persisted directories.

```
/home
/opt
/var/lib/docker
/var/lib/burmilla
```

<br>

> **Note:** When using a persistent console and in the current version's console, [rolling back](/docs/installation/upgrading#rolling-back-an-upgrade) is not supported. For example, rolling back to v0.4.5 when using a v0.5.0 persistent console is not supported.