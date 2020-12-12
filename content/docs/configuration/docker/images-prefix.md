---
title: Images prefix
weight: 1
bookToc: false
---
# Images prefix

When you have built your own docker registries, and have cached the `burmilla/os` and other `os-services` images,
something like a normal `docker pull burmilla/os` can be cached as `docker pull dockerhub.mycompanyname.com/docker.io/burmilla/os`.

However, you need a way to inject a prefix into BurmillaOS for installation or service pulls.
BurmillaOS supports a global prefix you can add to force ROS to always use your mirror.

You can config a global image prefix:

```shell
ros config set burmilla.environment.REGISTRY_DOMAIN xxxx.yyy
```

Then you check the os list:

```shell
$ ros os list
xxxx.yyy/burmilla/os:v1.3.0 remote latest running
xxxx.yyy/burmilla/os:v1.2.0 remote available
...
...
```

Also you can check consoles:

```shell
$ ros console switch ubuntu
Switching consoles will
1. destroy the current console container
2. log you out
3. restart Docker
Continue [y/N]: y
Pulling console (xxxx.yyy/burmilla/os-ubuntuconsole:v1.3.0)...
...
```

If you want to reset this setting:

```shell
ros config set burmilla.environment.REGISTRY_DOMAIN docker.io
```
