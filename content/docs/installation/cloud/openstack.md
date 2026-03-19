---
title: OpenStack
bookToc: false
---
# OpenStack

Because the BurmillaOS community is small, we do not currently publish a dedicated OpenStack image in our [releases](https://github.com/burmilla/os/releases). You can upload the `rootfs.tar.gz` artifact or build a custom QCOW2 image from the BurmillaOS ISO.

When launching an instance using a custom image, enable **Advanced Options** -> **Configuration Drive** in order to use a [cloud-config](/docs/configuration/base/#cloud-config) file.
