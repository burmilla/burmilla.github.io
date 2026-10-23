---
title: Using iSCSI
---
# Using iSCSI

RancherOS shipped a separate `open-iscsi` system service. BurmillaOS does not have that service anymore: the `default` console already contains `open-iscsi` (and since v3.0.x also `multipath-tools`), so the iSCSI tools are available in the console without enabling anything. The initiator daemon `iscsid` is however not started automatically.

## Starting iscsid on boot

Use `/etc/rc.local` to load the `iscsi_tcp` kernel module and start `iscsid` on every boot:

```yaml
#cloud-config
write_files:
- path: /etc/rc.local
  permissions: "0755"
  owner: root
  content: |
    #!/bin/bash
    modprobe iscsi_tcp
    if [ ! -f /etc/iscsi/initiatorname.iscsi ] || grep -q GenerateName /etc/iscsi/initiatorname.iscsi; then
      echo "InitiatorName=$(iscsi-iname)" > /etc/iscsi/initiatorname.iscsi
    fi
    /etc/init.d/iscsid start
    exit 0
```

The console image is built once for everybody, so the initiator name has to be generated on the host. The snippet above does that on the first boot and keeps it afterwards, because the console container is [persistent](/docs/installation/custom-builds/custom-console#console-persistence). If you need a fixed initiator name, write `/etc/iscsi/initiatorname.iscsi` with [`write_files`](/docs/configuration/advanced/write-files) instead.

After a reboot, check that the daemon runs:

```bash
$ pgrep -a iscsid
$ sudo iscsiadm -m session
```

## Logging in to a target

Discover the targets of a portal and log in to them as on any other Linux distribution:

```bash
$ sudo iscsiadm -m discovery -t sendtargets -p <portal-ip>
$ sudo iscsiadm -m node -T <target-iqn> -p <portal-ip> --login
```

To log in automatically on boot set `node.startup = automatic` for the node (`sudo iscsiadm -m node -T <target-iqn> -p <portal-ip> -o update -n node.startup -v automatic`) and run `/etc/init.d/open-iscsi start` after `iscsid` in `/etc/rc.local`. The node database lives in `/etc/iscsi` of the console, which survives reboots as long as the console container is not replaced (`ros console switch` recreates it).

The block device (`/dev/sd*`) shows up in the console and in the host, so it can be used for [additional mounts](/docs/storage/additional-mounts), by Docker volume plugins or, with `multipath-tools`, for multipath setups.

> **Note:** Docker volume plugins which need `iscsid` (e.g. storage vendor plugins) must be started after it. Put the `docker plugin enable` command into `/etc/rc.local` after `wait-for-docker`, see [running commands](/docs/configuration/advanced/running-commands).

## Longhorn and Kubernetes

Kubernetes and related tools such as [Longhorn](https://longhorn.io/) are not an officially [supported use case](/#supported-use-cases) of BurmillaOS. Users have reported that RKE with Longhorn works once `iscsid` is started as described above, but such setups are not tested by the BurmillaOS project.
