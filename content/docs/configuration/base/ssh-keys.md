---
title: SSH Settings
weight: 1
bookToc: false
---
# SSH Settings

BurmillaOS supports adding SSH keys through the [cloud-config](/docs/configuration/#cloud-config) file. Within the cloud-config file, you simply add the ssh keys within the `ssh_authorized_keys` key.

```yaml
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAA...ZZZ example1@burmilla
  - ssh-rsa BBB...ZZZ example2@burmilla
```

When we pass the cloud-config file during the `ros install` command, it will allow these ssh keys to be associated with the **rancher** user. You can ssh into BurmillaOS using the key.

```shell
$ ssh -i /path/to/private/key rancher@<ip-address>
```

Please note that OpenSSH 7.0 and greater similarly disable the ssh-dss (DSA) public key algorithm. It too is weak and we recommend against its use.

## SSHD Port and IP

BurmillaOS supports changing the sshd port and IP, you can use these in the cloud-config file:

```yaml
rancher:
  ssh:
    port: 10022
    listen_address: 172.22.100.100
```

These settings are only designed for default console.
Because if you change sshd-config, restart the host will restore the default, the new configuration will not take effect.

For other consoles, all files are persistent, you can modify sshd-config by yourself.
