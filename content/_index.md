# Overview of BurmillaOS

BurmillaOS is our reaction to the End of RancherOS, which was one of the smallest and easiest ways to run docker, as every process including services as `udev` or even `syslog` are running in their own containers. As the system is stripped of anything unnecessary to run docker, the resulting system is way smaller than most others of todays operating systems.

Speaking of security, the stripping of unneeded components also greatly reduces the amount of work going into security patching and other maintenance tasks. This is possible because, with Docker, users typically package all necessary libraries into their containers. As it is a total shame to see RancherOS vanish into thin air (read: end of life / maintenance), we decided to pick up where they left by still including the latest version of Docker to allow users to take the advantage of the latest Docker capabilities and fixes.

Unlike the big players, BurmillaOS boots very quick and is nearly instantly ready to fire up your container workloads.

To read more about Docker, please head over to [Docker user guide](https://docs.docker.com/config/daemon/).

**If you want to know the latest project news, we invite you to join [Discord](https://bit.ly/DiscordBurmillaOS)**

## Hardware Requirements

### Memory Requirements

| Platform   | RAM requirements |
| ---------- | ---------------- |
| Baremetal  | 1GB              |
| VirtualBox | 1GB              |
| VMWare     | 1GB              |
| GCE        | 1GB              |
| AWS        | 1GB              |

You can adjust memory requirements by custom building BurmillaOS, please refer to [reduce-memory-requirements](/docs/installation/custom-builds/custom-burmillaos-iso#reduce-memory-requirements)

## How BurmillaOS Works

Everything in BurmillaOS is a Docker container. We accomplish this by launching two instances of Docker. One is what we call **System Docker** and is the first process on the system. All other system services, like `ntpd`, `syslog`, and `console`, are running in Docker containers. System Docker replaces traditional init systems like `systemd` and is used to launch [additional system services](/docs/system-services/).

System Docker runs a special container called **Docker**, which is another Docker daemon responsible for managing all of the user’s containers. Any containers that you launch as a user from the console will run inside this Docker. This creates isolation from the System Docker containers and ensures that normal user commands don’t impact system services.

We created this separation not only for the security benefits, but also to make sure that commands like `docker rm -f $(docker ps -qa)` don't delete the entire OS.

![How it works](https://raw.githubusercontent.com/burmilla/burmilla.github.io/master/static/images/howitworks.png)

## Supported Use Cases

BurmillaOS is built around Docker and is best suited for the following workloads:

- Standalone Docker containers (`docker run` / `docker create`)
- Multi-container applications (`docker-compose`)
- Docker Swarm mode (`docker stack deploy` / `docker service`)

The following use cases are **not** the focus of BurmillaOS and issues related to them are considered low priority:

- Kubernetes / K3s (consider [k3OS](https://github.com/rancher/k3os) for those use cases)
- Rancher 2.x (requires Kubernetes)

Users are free to attempt these use cases, but they are not officially supported.

## Running BurmillaOS

To get started with BurmillaOS, head over to our [Quick Start Guide](/docs/quick-start-guide).

## Latest Release

Please check our [repository](https://github.com/burmilla/os/releases) for the latest [release](https://github.com/burmilla/os/releases).

## Community

* [GitHub Discussions](https://github.com/burmilla/os/discussions)
* [Discord Server](https://discord.com/invite/AR6daurAAk)
