---
title: OpenStack
bookToc: false
---
# OpenStack

BurmillaOS releases include an Openstack image that can be found on our [releases page](https://github.com/burmilla/os/releases). The image format is [QCOW3](https://wiki.qemu.org/Features/Qcow3#Fully_QCOW2_backwards-compatible_feature_set) that is backward compatible with QCOW2.

When launching an instance using the image, you must enable **Advanced Options** -> **Configuration Drive** and in order to use a [cloud-config](/docs/configuration/base/#cloud-config) file.
