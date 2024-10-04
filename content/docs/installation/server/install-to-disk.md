# Installing to Disk

BurmillaOS comes with a simple installer that will install BurmillaOS on a given target disk. To install BurmillaOS on a new disk, you can use the `ros install` command. Before installing, you'll need to have already [booted BurmillaOS from ISO](/docs/installation/workstation//boot-from-iso). Please be sure to pick the `burmillaos.iso` from our release [page](https://github.com/burmilla/os/releases).

## Installing BurmillaOS

The `ros install` command orchestrates the installation from the `burmilla/os` container. You will need to have already created a cloud-config file and found the target disk.

### Cloud-Config

The easiest way to log in is to pass a `cloud-config.yml` file containing your public SSH keys. To learn more about what's supported in our cloud-config, please read our [documentation](/docs/configuration/base/#cloud-config).

The `ros install` command will process your `cloud-config.yml` file specified with the `-c` flag. This file will also be placed onto the disk and installed to `/var/lib/rancher/conf/`. It will be evaluated on every boot.

Create a cloud-config file with a SSH key, this allows you to SSH into the box as the burmilla user. The yml file would look like this:

```yaml
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAA...
```

<br>

You can generate a new SSH key for `cloud-config.yml` file by following this [article](https://help.github.com/articles/generating-ssh-keys/).

Copy the public SSH key into BurmillaOS before installing to disk.

Now that our `cloud-config.yml` contains our public SSH key, we can move on to installing BurmillaOS to disk!

```bash
$ sudo ros install -c cloud-config.yml -d /dev/sda
INFO[0000] No install type specified...defaulting to generic
Installing from burmilla/os:v2.0.0
Continue [y/N]:
```

For the `cloud-config.yml` file, you can also specify a remote URL, but you need to make sure you can get it:

```bash
$ sudo ros install -c https://link/to/cloud-config.yml
```

You will be prompted to see if you want to continue. Type **y**.

```bash
Unable to find image 'burmilla/os:v2.0.0' locally
v2.0.0: Pulling from burmilla/os
...
...
...
Status: Downloaded newer image for burmilla/os:v2.0.0
+ DEVICE=/dev/sda
...
...
...
+ umount /mnt/new_img
Continue with reboot [y/N]:
```

After installing BurmillaOS to disk, you will no longer be automatically logged in as the `rancher` user. You'll need to have added in SSH keys within your [cloud-config file](/docs/configuration/base/#cloud-config).

### Installing a Different Version

By default, `ros install` uses the same installer image version as the ISO it is run from. The `-i` option specifies the particular image to install from. To keep the ISO as small as possible, the installer image is downloaded from DockerHub and used in System Docker. For example for BurmillaOS v2.0.0 the default installer image would be `burmilla/os:v2.0.0`.

You can use `ros os list` command to find the list of available BurmillaOS images/versions.

```bash
$ sudo ros os list
burmilla/os:v2.0.0 local
```

Alternatively, you can set the installer image to any image in System Docker to install BurmillaOS. This is particularly useful for machines that will not have direct access to the internet.

### Caching Images

_Available as of RancherOS v1.5.3_

Some configurations included in `cloud-config` require images to be downloaded from Docker to start. After installation, these images are downloaded automatically by BurmillaOS when booting. An example of these configurations are:

- rancher.services_include
- rancher.console
- rancher.docker

If you want to download and save these images to disk during installation, they will be cached and not need to be downloaded again upon each boot. You can cache these images by adding `-s` when using `ros install`:

```bash
$ ros install -d <disk> -c <cloud-config.yaml> -s
```

## SSH into BurmillaOS

After installing BurmillaOS, you can ssh into BurmillaOS using your private key and the **burmilla** user.

```bash
$ ssh -i /path/to/private/key rancher@<ip-address>
```

## Installing with no Internet Access

If you'd like to install BurmillaOS onto a machine that has no internet access, it is assumed you either have your own private registry or other means of distributing docker images to System Docker of the machine. If you need help with creating a private registry, please refer to the [Docker documentation for private registries](https://docs.docker.com/registry/).

In the installation command (i.e. `sudo ros install`), there is an option to pass in a specific image to install. As long as this image is available in System Docker, then BurmillaOS will use that image to install BurmillaOS.

```bash
$ sudo ros install -c cloud-config.yml -d /dev/sda -i <Image_Name_in_System_Docker>
INFO[0000] No install type specified...defaulting to generic
Installing from <Image_Name_in_System_Docker>
Continue [y/N]:
```
