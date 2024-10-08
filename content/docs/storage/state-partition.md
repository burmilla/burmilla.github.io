---
bookToc: false
---
# Persistent State Partition

BurmillaOS will store its state in a single partition specified by the `dev` field.  The field can be a device such as `/dev/sda1` or a logical name such `LABEL=state` or `UUID=123124`.  The default value is `LABEL=RANCHER_STATE`.  The file system type of that partition can be set to `auto` or a specific file system type such as `ext4`.

```yaml
#cloud-config
rancher:
  state:
    fstype: auto
    dev: LABEL=RANCHER_STATE
```

For other labels such as `RANCHER_BOOT` and `BURMILLA_OEM` and `BURMILLA_SWAP`, please refer to [Custom partition layout](/docs/storage/custom-partition-layout).

## Autoformat

You can specify a list of devices to check to format on boot. If the state partition is already found, BurmillaOS will not try to auto format a partition. By default, auto-formatting is off.

BurmillaOS will autoformat the partition to `ext4` (_not_ what is set in `fstype`) if the device specified in `autoformat`:

* Contains a boot2docker magic string
* Starts with 1 megabyte of zeros and `rancher.state.formatzero` is true


```yaml
#cloud-config
rancher:
  state:
    autoformat:
    - /dev/sda
    - /dev/vda
```
