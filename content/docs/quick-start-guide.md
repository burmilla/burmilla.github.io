---
weight: 1
---
# Quick Start

If you have a specific BurmillaOS [machine requirements](/#hardware-requirements), please check out our [guides on running BurmillaOS](/docs/installation). With the rest of this guide, we'll start up a BurmillaOS using [Docker machine](/docs/installation/workstation/docker-machine) and show you some of what BurmillaOS can do.

## Launching BurmillaOS using Docker Machine

Before moving forward, you'll need to have [Docker Machine](https://docs.docker.com/machine/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads) installed. Once you have VirtualBox and Docker Machine installed, it's just one command to get BurmillaOS running.

```bash
$ docker-machine create -d virtualbox \
        --virtualbox-boot2docker-url https://github.com/burmilla/os/releases/download/<version>/burmillaos.iso \
        --virtualbox-memory 2048 \
        <MACHINE-NAME>
```

That's it! You're up and running a BurmillaOS instance.

To log into the instance, just use the `docker-machine` command.

```bash
$ docker-machine ssh <MACHINE-NAME>
```

## A First Look At BurmillaOS

There are two Docker daemons running in BurmillaOS. The first is called **System Docker**, which is where BurmillaOS runs system services like ntpd and syslog. You can use the `system-docker` command to control the **System Docker** daemon.

The other Docker daemon running on the system is **Docker**, which can be accessed by using the normal `docker` command.

When you first launch BurmillaOS, there are no containers running in the Docker daemon. However, if you run the same command against the System Docker, you’ll see a number of system services that are shipped with BurmillaOS.

> **Note:** `system-docker` can only be used by root, so it is necessary to use the `sudo` command whenever you want to interact with System Docker.

```bash
$ sudo system-docker ps
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS               NAMES
07135915b03a        burmilla/os-docker:19.03.13         "ros user-docker"        15 hours ago        Up 15 hours                             docker
896c6169c2d5        burmilla/os-console:v2.0.0          "/usr/bin/ros entr..."   41 hours ago        Up 24 hours                             console
74e57e5940de        burmilla/os-base:v2.0.0             "/usr/bin/ros entr..."   41 hours ago        Up 24 hours                             ntp
989e8f137fb7        burmilla/os-base:v2.0.0             "/usr/bin/ros entr..."   41 hours ago        Up 24 hours                             network
79b750fa577a        burmilla/os-base:v2.0.0             "/usr/bin/ros entr..."   41 hours ago        Up 24 hours                             udev
29c582619c67        burmilla/container-crontab:v0.5.0   "container-crontab"      41 hours ago        Up 24 hours                             system-cron
cdd49fa26ecb        burmilla/os-syslog:v2.0.0           "/usr/bin/entrypoi..."   41 hours ago        Up 24 hours                             syslog
e490108ce8da        burmilla/os-acpid:v2.0.0            "/usr/bin/ros entr..."   41 hours ago        Up 24 hours                             acpid
```

Some containers are run at boot time, and others, such as the `console`, `docker`, etc. containers are always running.

## Using BurmillaOS

### Deploying a Docker Container

Let's try to deploy a normal Docker container on the Docker daemon.  The BurmillaOS Docker daemon is identical to any other Docker environment, so all normal Docker commands work.

```bash
$ docker run -d nginx
```

You can see that the nginx container is up and running:

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
e99c2c4b8b30        nginx               "nginx -g 'daemon off"   12 seconds ago      Up 11 seconds       80/tcp, 443/tcp     drunk_ptolemy
```

### Deploying A System Service Container

The following is a simple Docker container to set up Linux-dash, which is a minimal low-overhead web dashboard for monitoring Linux servers. The Dockerfile will be like this:

```Dockerfile
FROM hwestphal/nodebox
MAINTAINER hussein.galal.ahmed.11@gmail.com

RUN opkg-install unzip
RUN curl -k -L -o master.zip https://github.com/afaqurk/linux-dash/archive/master.zip
RUN unzip master.zip
WORKDIR linux-dash-master
RUN npm install

ENTRYPOINT ["node","server"]
```

Using the `hwestphal/nodebox` image, which uses a Busybox image and installs `node.js` and `npm`. We downloaded the source code of Linux-dash, and then ran the server. Linux-dash will run on port 80 by default.

To run this container in System Docker use the following command:

```bash
$ sudo system-docker run -d --net=host --name busydash husseingalal/busydash
```
In the command, we used `--net=host` to tell System Docker not to containerize the container's networking, and use the host’s networking instead. After running the container, you can see the monitoring server by accessing `http://<IP_OF_MACHINE>`.

![System Docker Container](/images/busydash.png)


To make the container survive during the reboots, you can create the `/opt/burmilla/bin/start.sh` script, and add the Docker start line to launch the Docker at each startup.

```bash
$ sudo mkdir -p /opt/burmilla/bin
$ echo "sudo system-docker start busydash" | sudo tee -a /opt/burmilla/bin/start.sh
$ sudo chmod 755 /opt/burmilla/bin/start.sh
```

### Using ROS

Another useful command that can be used with BurmillaOS is `ros` which can be used to control and configure the system.

```bash
$ sudo ros -v
version v2.0.0 from os image burmilla/os:v2.0.0
```

BurmillaOS state is controlled by a cloud config file. `ros` is used to edit the configuration of the system, to see for example the dns configuration of the system:

```bash
$ sudo ros config get rancher.network.dns.nameservers
- 8.8.8.8
- 8.8.4.4
```


When using the native Busybox console, any changes to the console will be lost after reboots, only changes to `/home` or `/opt` will be persistent. You can use the `ros console switch` command to switch to a [persistent console](/docs/installation/custom-builds/custom-console#console-persistence) and replace the native Busybox console. For example, to switch to the Ubuntu console:

```bash
$ sudo ros console switch ubuntu
```

## Conclusion

BurmillaOS is a simple Linux distribution ideal for running Docker.  By embracing containerization of system services and leveraging Docker for management, BurmillaOS hopes to provide a very reliable, and easy to manage OS for running containers.
