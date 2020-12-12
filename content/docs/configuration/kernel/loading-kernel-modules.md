---
title: Loading Kernel Modules
weight: 1
---
# Loading Kernel Modules

Since BurmillaOS v0.8, we build our own kernels using an unmodified kernel.org LTS kernel.
We provide both loading kernel modules with parameters and loading extra kernel modules for you.

## Loading Kernel Modules with parameters

The `burmilla.modules` can help you to set kernel modules or module parameters.

As an example, I'm going to set a parameter for kernel module `ndb`

```shell
$ sudo ros config set burmilla.modules "['nbd nbds_max=1024', 'nfs']"
```

Or

```yaml
#cloud-config
rancher:
  modules: [nbd nbds_max=1024, nfs]
```

After rebooting, you can check that `ndbs_max` parameter has been updated.

```shell
$ sudo cat /sys/module/nbd/parameters/nbds_max
1024
```

## Loading Extra Kernel Modules

We also build almost all optional extras as modules - so most in-tree modules are available
in the `kernel-extras` service.

If you do need to build kernel modules for BurmillaOS, there are 4 options:

* Try the `kernel-extras` service
* Ask us to add it into the next release
* If its out of tree, copy the methods used for the zfs and open-iscsi services
* Build it yourself.

### Try the kernel-extras service

We build the BurmillaOS kernel with most of the optional drivers as kernel modules, packaged
into an optional BurmillaOS service.

To install these, run:

```shell
$ sudo ros service enable kernel-extras
$ sudo ros service up kernel-extras
```

The modules should now be available for you to `modprobe`

### Ask us to do it

Open a GitHub issue in the https://github.com/burmilla/os repository - we'll probably add
it to the kernel-extras next time we build a kernel. Tell us if you need the module at initial
configuration or boot, and we can add it to the default kernel modules.

### Copy the out of tree build method

See https://github.com/burmilla/os-services/blob/master/z/zfs.yml and
https://github.com/burmilla/os-services/tree/master/images/20-zfs

The build container and build.sh script build the source, and then create a tools image, which is used to
"wonka.sh" import those tools into the console container using `docker run`

### Build your own.

As an example I'm going build the `intel-ishtp` hid driver using the `burmilla/os-zfs:<version>` images to build in, as they should contain the right tools versions for that kernel.

```shell
sudo docker run --rm -it --entrypoint bash --privileged -v /lib:/host/lib -v $(pwd):/data -w /data burmilla/os-zfs:$(ros -v | cut -d ' ' -f 2)

apt-get update
apt-get install -qy libncurses5-dev bc libssh-dev
curl -SsL -o src.tgz https://github.com/burmilla/os-kernel/releases/download/v$(uname -r)/linux-$(uname -r)-src.tgz
tar zxvf src.tgz
zcat /proc/config.gz >.config
# Yes, ignore the name of the directory :/
cd v*
# enable whatever modules you want to add.
make menuconfig
# I finally found an Intel sound hub that wasn't enabled yet
# CONFIG_INTEL_ISH_HID=m
make modules SUBDIRS=drivers/hid/intel-ish-hid

# test it
insmod drivers/hid/intel-ish-hid/intel-ishtp.ko
rmmod intel-ishtp

# install it
ln -s /host/lib/modules/ /lib/
cp drivers/hid/intel-ish-hid/*.ko /host/lib/modules/$(uname -r)/kernel/drivers/hid/
depmod

# done
exit
```

Then in your console, you should be able to run

```shell
modprobe intel-ishtp
```
