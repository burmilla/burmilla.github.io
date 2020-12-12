---
title: Switching Docker Versions
weight: 1
---
# Switching Docker Versions

The version of User Docker used in BurmillaOS can be configured using a [cloud-config](/docs/configuration/#cloud-config) file or by using the `ros engine` command.

> **Note:** There are known issues in Docker when switching between versions. For production systems, we recommend setting the Docker engine only once [using a cloud-config](#setting-the-docker-engine-using-cloud-config).

## Available Docker engines

The `ros engine list` command can be used to show which Docker engines are available to switch to. This command will also provide details of which Docker engine is currently being used.

```shell
$ sudo ros engine list --update
current  docker-19.03.13
disabled docker-19.03.14
disabled docker-20.10.0
```

## Setting the Docker engine using cloud-config

BurmillaOS supports defining which Docker engine to use through the cloud-config file. To change the Docker version from the default packaged version, you can use the following cloud-config setting and select one of the available engines. In the following example, we'll use the cloud-config file to set BurmillaOS to use Docker 1.10.3 for User Docker.

```yaml
#cloud-config
rancher:
  docker:
    engine: docker-19.03.13
```

## Changing Docker engines after BurmillaOS has started

If you've already started BurmillaOS and want to switch Docker engines, you can change the Docker engine by using the `ros engine switch` command. In our example, we'll switch to Docker 19.03.13.

```shell
$ sudo ros engine switch docker-19.03.13
INFO[0001] Project [os]: Starting project
INFO[0003] [0/18] [docker]: Starting
INFO[0003] Recreating docker
INFO[0003] [1/18] [docker]: Started
INFO[0003] Project [os]: Project started

$ docker version
Client: Docker Engine - Community
 Version:           19.03.13
 API version:       1.40
 Go version:        go1.13.15
 Git commit:        4484c46
 Built:             Wed Sep 16 16:58:04 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.13
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       4484c46
  Built:            Wed Sep 16 17:04:43 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.3.7
  GitCommit:        8fba4e9a7d01810a393d5d25a3621dc101981175
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683

```

### Enabling Docker engines

If you don't want to automatically switch Docker engines, you can also set which version of Docker to use after the next reboot by enabling a Docker engine.

```
$ sudo ros engine enable docker-19.03.13
```

## Using a Custom Version of Docker

If you're using a version of Docker that isn't available by default or a custom build of Docker then you can create a custom Docker image and service file to distribute it.

Docker engine images are built by adding the binaries to a folder named `engine` and then adding this folder to a `FROM scratch` image. For example, the following Dockerfile will build a Docker engine image.

```Dockerfile
FROM scratch
COPY engine /engine
```

Once the image is built a [system service](/docs/system-services/) configuration file must be created. An [example file](https://github.com/burmilla/os-services/blob/master/d/docker-19.03.13.yml) can be found in the burmilla/os-services repo. Change the `image` field to point to the Docker engine image you've built.

All of the previously mentioned methods of switching Docker engines are now available. For example, if your service file is located at `https://myservicefile` then the following cloud-config file could be used to use your custom Docker engine.

```yaml
#cloud-config
rancher:
  docker:
    engine: https://myservicefile
```
