---
title: iPXE
bookToc: false
---
# iPXE

```bash
#!ipxe
# Boot a persistent BurmillaOS to RAM

# Location of Kernel/Initrd images
set base-url <url>

kernel ${base-url}/vmlinuz rancher.state.dev=LABEL=RANCHER_STATE rancher.state.autoformat=[/dev/sda] rancher.state.wait rancher.cloud_init.datasources=[url:http://example.com/cloud-config]
initrd ${base-url}/initrd
boot
```

If you want to autoformat the disk when booting by iPXE, you should add the `rancher.state.autoformat` part to kernel cmdline. However, this does not install the bootloader to disk, so you cannot upgrade BurmillaOS.

If you don't add `rancher.state.autoformat`, BurmillaOS will run completely in memory, you can execute `ros install` to install to disk.

## Hiding sensitive kernel commandline parameters

_Available as of RancherOS v0.9_

Secrets can be put on the `kernel` parameters line afer a `--` double dash, and they will be not be shown in any `/proc/cmdline`. These parameters
will be passed to the BurmillaOS init process and stored in the `root` accessible `/var/lib/rancher/conf/cloud-init.d/init.yml` file, and are available to the root user from the `ros config` commands.

For example, the `kernel` line above could be written as:

```bash
kernel ${base-url}/vmlinuz rancher.state.dev=LABEL=RANCHER_STATE rancher.state.autoformat=[/dev/sda] -- rancher.cloud_init.datasources=[url:http://example.com/cloud-config]
```

The hidden part of the command line can be accessed with either `sudo ros config get rancher.environment.EXTRA_CMDLINE`, or by using a service file's environment array.

An example service.yml file:

```yaml
test:
  image: alpine
  command: echo "tell me a secret ${EXTRA_CMDLINE}"
  labels:
    io.rancher.os.scope: system
  environment:
  - EXTRA_CMDLINE
```

When this service is run, the `EXTRA_CMDLINE` will be set.


### cloud-init Datasources

Valid cloud-init datasources for BurmillaOS.

| type | default |
|---|---|
| ec2 | Default metadata address |
| digitalocean | Default metadata address |
| packet | Default metadata address |
| cloudstack | Default metadata address |
| aliyun | Default metadata address |
| gce | Default metadata address |
| file | Path |
| cmdline | Kernel command line: `cloud-config-url=http://link/user_data` |
| configdrive | /media/config-2 |
| url | URL address |
| vmware| Set `guestinfo` cloud-init or interface data as per [VMware ESXi](/docs/installation/cloud/vmware-esxi) |
| * | This will add ["configdrive", "vmware", "ec2", "digitalocean", "packet", "gce"] into the list of datasources to try |

The vmware datasource was added as of v1.1.

### Cloud-Config

When booting via iPXE, BurmillaOS can be configured using a [cloud-config file](/docs/configuration/base/#cloud-config).
