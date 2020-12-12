---
bookCollapseSection: true
bookToc: false
---
# Built-in System Services

To launch BurmillaOS, we have built-in system services. They are defined in the [Docker Compose](https://docs.docker.com/compose/compose-file/) format, and can be found in the default system config file, `/usr/share/ros/os-config.yml`. You can [add your own system services](/docs/system-services/) or override services in the cloud-config.

## Preloading User Images

Read more about [image preloading](/docs/installation/boot-process/image-preloading).

## Network

During this service, networking is set up, e.g. hostname, interfaces, and DNS.

It is configured by `hostname` and `rancher.network`settings in [cloud-config](/docs/configuration/base/#cloud-config).

## NTP

Runs `ntpd` in a System Docker container.

## Console

This service provides the BurmillaOS user interface by running `sshd` and `getty`. It completes the BurmillaOS configuration on start up:

1. If the `rancher.password=<password>` kernel parameter exists, it sets `<password>` as the password for the `burmilla` user.
2. If there are no host SSH keys, it generates host SSH keys and saves them under `rancher.ssh.keys` in [cloud-config](/docs/configuration/base/#cloud-config).
3. Runs `cloud-init -execute`, which does the following:
   * Updates `.ssh/authorized_keys` in `/home/burmilla` and `/home/docker` in the [cloud-config](/docs/configuration/base/ssh-keys) and metadata.
   * Writes files specified by setting `write_files` in the [cloud-config](/docs/configuration/advanced/write-files).
   * Resizes the device specified by setting `rancher.resize_device` in the [cloud-config](/docs/configuration/advanced/resizing-device-partition).
   * Mount devices specified in the `mounts` in the [cloud-config](/docs/storage/additional-mounts).
   * Set sysctl parameters specified in  the`rancher.sysctl` [cloud-config](/docs/configuration/advanced/sysctl).
4. If user-data contained a file that started with `#!`, then a file would be saved at `/var/lib/burmilla/conf/cloud-config-script` during cloud-init and then executed. Any errors are ignored.
5. Runs `/opt/burmilla/bin/start.sh` if it exists and is executable. Any errors are ignored.
6. Runs `/etc/rc.local` if it exists and is executable. Any errors are ignored.

## Docker

This system service runs the user docker daemon. Normally it runs inside the console system container by running `docker-init` script which, in turn, looks for docker binaries in `/opt/bin`, `/usr/local/bin` and `/usr/bin`, adds the first found directory with docker binaries to PATH and runs `dockerlaunch docker daemon` appending the passed arguments.

Docker daemon args are read from `rancher.docker.args` cloud-config property (followed by `rancher.docker.extra_args`).
