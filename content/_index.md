# Overview of BurmillaOS

BurmillaOS is the smallest, easiest way to run Docker in production.  Every process in BurmillaOS is a container managed by Docker. This includes system services such as `udev` and `syslog`.  Because it only includes the services necessary to run Docker, BurmillaOS is significantly smaller than most traditional operating systems. By removing unnecessary libraries and services, requirements for security patches and other maintenance are also reduced. This is possible because, with Docker, users typically package all necessary libraries into their containers.

Another way in which BurmillaOS is designed specifically for running Docker is that it always runs the latest version of Docker. This allows users to take advantage of the latest Docker capabilities and bug fixes.

Like other minimalist Linux distributions, BurmillaOS boots incredibly quickly. Starting Docker containers is nearly instant, similar to starting any other process. This speed is ideal for organizations adopting microservices and autoscaling.

Docker is an open-source platform designed for developers, system admins, and DevOps. It is used to build, ship, and run containers, using a simple and powerful command line interface (CLI). To get started with Docker, please visit the [Docker user guide](https://docs.docker.com/engine/userguide/).

## Hardware Requirements

### Memory Requirements

Platform   | RAM requirements
--------   | ------------------------
Baremetal  | 1GB
VirtualBox | 1GB
VMWare     | 1GB
GCE        | 1GB
AWS        | 1GB

You can adjust memory requirements by custom building BurmillaOS, please refer to [reduce-memory-requirements](/docs/installation/custom-builds/custom-burmillaos-iso#reduce-memory-requirements)

## How BurmillaOS Works

Everything in BurmillaOS is a Docker container. We accomplish this by launching two instances of Docker. One is what we call **System Docker** and is the first process on the system. All other system services, like `ntpd`, `syslog`, and `console`, are running in Docker containers. System Docker replaces traditional init systems like `systemd` and is used to launch [additional system services](/docs/system-services/).

System Docker runs a special container called **Docker**, which is another Docker daemon responsible for managing all of the user’s containers. Any containers that you launch as a user from the console will run inside this Docker. This creates isolation from the System Docker containers and ensures that normal user commands don’t impact system services.

 We created this separation not only for the security benefits, but also to make sure that commands like `docker rm -f $(docker ps -qa)` don't delete the entire OS.

![How it works](https://raw.githubusercontent.com/burmilla/burmilla.github.io/master/img/howitworks.png)

## Running BurmillaOS

To get started with BurmillaOS, head over to our [Quick Start Guide](/docs/quick-start-guide).

## Latest Release

Please check our [repository](https://github.com/burmilla/os/releases) for the latest [release](https://github.com/burmilla/os/releases).
