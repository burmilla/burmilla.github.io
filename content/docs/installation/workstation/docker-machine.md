# Using Docker Machine

Before we get started, you'll need to make sure that you have docker machine installed. Download it directly from the docker machine [releases](https://github.com/docker/machine/releases).
You also need to know the [memory requirements](#hardware-requirements).

> **Note:** If you create a BurmillaOS instance using Docker Machine, you will not be able to upgrade your version of BurmillaOS.

## Downloading BurmillaOS

Get the latest ISO artifact from the BurmillaOS [releases](https://github.com/burmilla/os).

Machine Driver | Recommended BurmillaOS version | ISO File
-------------- | ----------------------------- | -------------------------------------------------------------
VirtualBox     | >=v1.9.0 | burmillaos.iso
VMWare VSphere | >=v1.9.0 | burmillaos-autoformat.iso
VMWare Fusion  | >=v1.9.0 | burmillaos-autoformat.iso
Hyper-V        | >=v1.9.0 | burmillaos.iso
Proxmox VE     | >=v1.9.0 | burmillaos-autoformat.iso

## Docker Machine

You can use Docker Machine to launch VMs for various providers. Currently VirtualBox and VMWare(VMWare VSphere, VMWare Fusion) and AWS are supported.

### Using VirtualBox

Before moving forward, you'll need to have VirtualBox installed. Download it directly from [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Once you have VirtualBox and Docker Machine installed, it's just one command to get BurmillaOS running.

Here is an example about using the BurmillaOS latest link:

```bash
$ docker-machine create -d virtualbox \
        --virtualbox-boot2docker-url https://github.com/burmilla/os/releases/download/<version>/burmillaos.iso \
        --virtualbox-memory <MEMORY-SIZE> \
        <MACHINE-NAME>
```

> **Note:** Instead of downloading the ISO, you can directly use the URL for the `burmillaos.iso`.

That's it! You should now have a BurmillaOS host running on VirtualBox. You can verify that you have a VirtualBox VM running on your host.

> **Note:** After the machine is created, Docker Machine may display some errors regarding creation, but if the VirtualBox VM is running, you should be able to [log in](#logging-into-burmillaos).

```bash
$ VBoxManage list runningvms | grep <MACHINE-NAME>
```

This command will print out the newly created machine. If not, something went wrong with the provisioning step.

### Using VMWare VSphere

Before moving forward, you’ll need to have VMWare VSphere installed. Once you have VMWare VSphere and Docker Machine installed, it’s just one command to get BurmillaOS running.

Here is an example about using the BurmillaOS latest link:

```bash
$ docker-machine create -d vmwarevsphere \
        --vmwarevsphere-username <USERNAME> \
        --vmwarevsphere-password <PASSWORD> \
        --vmwarevsphere-memory-size <MEMORY-SIZE> \
        --vmwarevsphere-boot2docker-url https://github.com/burmilla/os/releases/download/<version>/burmillaos-autoformat.iso \
        --vmwarevsphere-vcenter <IP-ADDRESS> \
        --vmwarevsphere-vcenter-port <PORT> \
        --vmwarevsphere-disk-size <DISK-SIZE> \
        <MACHINE-NAME>
```

That’s it! You should now have a BurmillaOS host running on VMWare VSphere. You can verify that you have a VMWare(ESXi) VM running on your host.

### Using VMWare Fusion

Before moving forward, you’ll need to have VMWare Fusion installed. Once you have VMWare Fusion and Docker Machine installed, it’s just one command to get BurmillaOS running.

Here is an example about using the BurmillaOS latest link:

```bash
$ docker-machine create -d vmwarefusion \
        --vmwarefusion-no-share \
        --vmwarefusion-memory-size <MEMORY> \
        --vmwarefusion-boot2docker-url https://github.com/burmilla/os/releases/download/<version>/burmillaos-autoformat.iso \
        <MACHINE_NAME>
```

That’s it! You should now have a BurmillaOS host running on VMWare Fusion. You can verify that you have a VMWare Fusion VM running on your host.

### Using Hyper-V

You should refer to the documentation of [Hyper-V driver](https://docs.docker.com/machine/drivers/hyper-v/), here is an example of using the latest BurmillaOS URL. We recommend using a specific version so you know which version of BurmillaOS that you are installing.

```bash
$ docker-machine.exe create -d hyperv \
        --hyperv-memory 2048 \
        --hyperv-boot2docker-url https://github.com/burmilla/os/releases/download/<version>/burmillaos.iso
        --hyperv-virtual-switch <SWITCH_NAME> \
        <MACHINE_NAME>
```
### Using Proxmox VE

There is currently no official Proxmox VE driver, but there is a [choice](https://github.com/lnxbil/docker-machine-driver-proxmox-ve) that you can refer to.

## Logging into BurmillaOS

Logging into BurmillaOS follows the standard Docker Machine commands. To login into your newly provisioned BurmillaOS VM.

```bash
$ docker-machine ssh <MACHINE-NAME>
```

You'll be logged into BurmillaOS and can start exploring the OS, This will log you into the BurmillaOS VM. You'll then be able to explore the OS by [adding system services](/docs/system-services/), [customizing the configuration](/docs/configuration/base), and launching containers.

If you want to exit out of BurmillaOS, you can exit by pressing `Ctrl+D`.

## Docker Machine Benefits

With Docker Machine, you can point the docker client on your host to the docker daemon running inside of the VM. This allows you to run your docker commands as if you had installed docker on your host.

To point your docker client to the docker daemon inside the VM, use the following command:

```bash
$ eval $(docker-machine env <MACHINE-NAME>)
```

After setting this up, you can run any docker command in your host, and it will execute the command in your BurmillaOS VM.

```bash
$ docker run -p 80:80 -p 443:443 -d nginx
```

In your VM, a nginx container will start on your VM. To access the container, you will need the IP address of the VM.

```bash
$ docker-machine ip <MACHINE-NAME>
```

Once you obtain the IP address, paste it in a browser and a _Welcome Page_ for nginx will be displayed.
