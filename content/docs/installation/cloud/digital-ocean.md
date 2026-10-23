---
bookToc: false
---
# Digital Ocean

Because the BurmillaOS community is small, we do not publish cloud specific images in our [releases](https://github.com/burmilla/os/releases), and BurmillaOS is not offered in the Digital Ocean portal. Only the generic ISO is published. You can build your own image for Digital Ocean from it with the scripts in the [burmilla/os-packer](https://github.com/burmilla/os-packer) repository and upload it as a [custom image](https://docs.digitalocean.com/products/images/custom-images/). This also lets you choose settings like the disk size yourself.

>**Note**
>Deploying to Digital Ocean will incur charges.

## Building the image

The `digitalocean` directory of `os-packer` contains a [Packer](https://www.packer.io/) template which boots the BurmillaOS ISO in QEMU, installs it to a virtual disk with the `digitalocean` cloud-init datasource enabled and produces a `qcow2` image. It needs a Linux machine with KVM, for example a Droplet of the Ubuntu Docker image with the `ufw` firewall disabled. The steps below were reported to work by a user:

```bash
$ sudo apt-get install -y packer qemu-kvm
$ git clone https://github.com/burmilla/os-packer.git
$ cd os-packer/digitalocean
# The template was written for RancherOS, download the BurmillaOS ISO under the name it expects
$ wget -O rancheros.iso https://github.com/burmilla/os/releases/download/<version>/burmillaos.iso
```

Edit `digitalocean-qemu.json` so that `iso_url` points to `./rancheros.iso`, and raise the QEMU memory (`-m`) if the build machine has more RAM. Then build:

```bash
$ ISO_CHECKSUM=$(md5sum rancheros.iso | cut -d' ' -f1) PACKER_BOOT_WAIT=180s PACKER_LOG=1 packer build digitalocean-qemu.json
```

The image is written to `output-digitalocean/`. Copy it to your workstation, or upload it directly from the build machine.

## Uploading and starting a Droplet

1. In the Digital Ocean portal, go to **Images** -> **Custom Images** and upload the `qcow2` file (or import it from a URL).
1. Click **New Droplet** and select the uploaded image under **Custom images**.
1. Choose a plan. Make sure your Droplet has the [minimum hardware requirements for BurmillaOS](/#hardware-requirements).
1. Optional: In the **Select additional options** section, check the **User data** box and enter a `cloud-config` file in the text box that appears. The `cloud-config` file is used to provide configuration on the first boot. An example is below.
1. Choose an SSH key that you have access to, or generate a new SSH key. BurmillaOS reads the keys from the Digital Ocean metadata service.
1. Click **Create.**

You can access the host via SSH after the Droplet is booted. The default user is `rancher`.

Below is an example `cloud-config` file that you can use to initialize the Droplet with user data, such as starting a container on boot:

```yaml
#cloud-config

write_files:
  - path: /etc/rc.local
    permissions: "0755"
    owner: root
    content: |
      #!/bin/bash
      wait-for-docker
      docker run -d --restart=unless-stopped -p 80:80 nginx
```
