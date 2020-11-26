# Overview of BurmillaOS

BurmillaOS is our reaction of us to the End of RancherOS, which was one of the smallest and easiest ways to run docker as every process including services as `udev` or even `syslog` are running in their own containers. As the system is stripped of anything unnecessary to run docker, the resulting system is way smaller than most others of todays operating systems.

Speaking of security, the stripping of unneeded components also greatly reduces the ammount of work going into security patching and other maintenance tasks. This is possible because, with Docker, users typically package all necessary libraries into their containers. As it is a total shame to see RancherOS vanish into thin air (read: end of life / maintenance), we decided to pick up where they left by still including the latest version of Docker to allow users to take the advantage of the latest Docker capabilities and fixes.

Unlike the big players, BurmillaOS boots very quick and is nearly instantly ready to fire up your container workloads.

To read more about Docker, please head over to [Docker user guide](https://docs.docker.com/config/daemon/).

### Hardware Requirements

* Memory Requirements

Platform   | RAM requirement(>=v1.5.x) | RAM requirement(v1.4.x)
--------   | ------------------------  | ---------------------------
Baremetal  | 1GB                       | 1280MB
VirtualBox | 1GB                       | 1280MB
VMWare     | 1GB                       | 1280MB (burmillaos.iso) <br> 2048MB (burmillaos-vmware.iso)
GCE        | 1GB                       | 1280MB
AWS        | 1GB                       | 1.7GB

You can adjust memory requirements by custom building BurmillaOS, please refer to [reduce-memory-requirements](/installation/custom-builds/custom-burmillaos-iso/#reduce-memory-requirements)

### How BurmillaOS Works

Everything in BurmillaOS is a Docker container. We accomplish this by launching two instances of Docker. One is what we call **System Docker** and is the first process on the system. All other system services, like `ntpd`, `syslog`, and `console`, are running in Docker containers. System Docker replaces traditional init systems like `systemd` and is used to launch [additional system services](/system-services/).

System Docker runs a special container called **Docker**, which is another Docker daemon responsible for managing all of the user’s containers. Any containers that you launch as a user from the console will run inside this Docker. This creates isolation from the System Docker containers and ensures that normal user commands don’t impact system services.

 We created this separation not only for the security benefits, but also to make sure that commands like `docker rm -f $(docker ps -qa)` don't delete the entire OS.

![How it works](https://raw.githubusercontent.com/burmilla/burmilla.github.io/master/img/howitworks.png)


### Running BurmillaOS

To get started with BurmillaOS, head over to our [Quick Start Guide](/configurationquick-start-guide/).

### Latest Release

Please check our repository for the latest release in our [README](https://github.com/burmilla/os/blob/master/README.md).

<br>
<br>
