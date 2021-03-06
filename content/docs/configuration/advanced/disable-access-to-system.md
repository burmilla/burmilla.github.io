---
title: Disabling Access to BurmillaOS
weight: 1
bookToc: false
---
# Disabling Access to BurmillaOS
In BurmillaOS, you can set `rancher.password` as a kernel parameter and `auto-login` to be enabled, but there may be some cases where we want to disable both of these options. Both of these options can be disabled in the cloud-config or as part of a `ros` command.

## How to Disabling Options

If BurmillaOS has already been started, you can use `ros config set` to update that you want to disable

```shell
# Disabling the `rancher.password` kernel parameter
$ sudo ros config set rancher.disable ["password"]

# Disabling the `autologin` ability
$ sudo ros config set rancher.disable ["autologin"]
```

Alternatively, you can set it up in your cloud-config so it's automatically disabled when you boot BurmillaOS.


```yaml
# cloud-config
rancher:
  disable:
  - password
  - autologin
```
