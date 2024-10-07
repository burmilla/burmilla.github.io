# Custom Console

When [booting from the ISO](/docs/installation/workstation/boot-from-iso), BurmillaOS starts with the `default` console, which is based on [`debian:buster-slim`](https://github.com/burmilla/os/blob/v1.9.x/images/02-console/Dockerfile).

No other console is officially supported.

### Using the unofficial custom console images

If you want to use other console, the most easy way is use the [unofficial custom console images](https://github.com/benok/burmilla-os-console).

To use this, you need to add the following settings to your cloud-init.yml.

```
rancher:
  repositories:
     console:
       url: https://raw.githubusercontent.com/benok/burmilla-os-console/master
```

With the settings above, you can select which console you want BurmillaOS to start with using the [cloud-config](/docs/configuration/#cloud-config).

If you want to create your own custom console, please check [this page](https://github.com/benok/burmilla-os-console#how-to-build-your-own-console-and-use).

### Enabling Consoles using Cloud-Config

When launching BurmillaOS with a [cloud-config](/docs/configuration/#cloud-config) file, you can select which console you want to use.

Currently, the list of available consoles (with above setting using the unofficial console images) are:

* official image
  * default (debian:buster-slim)
* unofficial images
  * debian (debian:buster)
  * debian_testing (debian:testing)
  * ubuntu (ubuntu:latest)
  * alpine (alpine:latest)
  * fedora (fedora:latest)

Here is an example cloud-config file that can be used to enable the debian console.

```yaml
#cloud-config
rancher:
  console: debian
```

### Listing Available Consoles

You can easily list the available consoles in BurmillaOS and what their status is with `sudo ros console list`.

```
$ sudo ros console list
disabled alpine
disabled debian
disabled debian_testing
enabled default
disabled fedora
disabled ubuntu
```

### Changing Consoles after BurmillaOS has started (not recomemded)

**`ros console switch` has several bugs since RancherOS era, please use "[Enabling consoles](#enabling-consoles)" below**.

You can view which console is being used by BurmillaOS by checking which console container is running in System Docker. If you wanted to switch consoles, you just need to run a simple command and select your new console.

For our example, we'll switch to the Ubuntu console.

```
$ sudo ros console switch ubuntu
Switching consoles will
1. destroy the current console container
2. log you out
3. restart Docker
Continue [y/N]:y
Pulling console (burmilla/os-ubuntuconsole:v0.5.0-3)...
v0.5.0-3: Pulling from burmilla/os-ubuntuconsole
6d3a6d998241: Pull complete
606b08bdd0f3: Pull complete
1d99b95ffc1c: Pull complete
a3ed95caeb02: Pull complete
3fc2f42db623: Pull complete
2fb84911e8d2: Pull complete
fff5d987b31c: Pull complete
e7849ae8f782: Pull complete
de375d40ae05: Pull complete
8939c16614d1: Pull complete
Digest: sha256:37224c3964801d633ea8b9629137bc9d4a8db9d37f47901111b119d3e597d15b
Status: Downloaded newer image for burmilla/os-ubuntuconsole:v0.5.0-3
switch-console_1 | time="2016-07-02T01:47:14Z" level=info msg="Project [os]: Starting project "
switch-console_1 | time="2016-07-02T01:47:14Z" level=info msg="[0/18] [console]: Starting "
switch-console_1 | time="2016-07-02T01:47:14Z" level=info msg="Recreating console"
Connection to 127.0.0.1 closed by remote host.
```

<br>

After logging back, you'll be in the Ubuntu console.

```
$ sudo system-docker ps
CONTAINER ID        IMAGE                                 COMMAND                  CREATED              STATUS              PORTS               NAMES
6bf33541b2dc        burmilla/os-ubuntuconsole:v0.5.0-rc3   "/usr/sbin/entry.sh /"   About a minute ago   Up About a minute
```

<br>

> **Note:** When switching between consoles, the currently running console container is destroyed, Docker is restarted and you will be logged out.

### Console persistence

All consoles except the default (busybox) console are persistent. Persistent console means that the console container will remain the same and preserves changes made to its filesystem across reboots. If a container is deleted/rebuilt, state in the console will be lost except what is in the persisted directories.

```
/home
/opt
/var/lib/docker
/var/lib/rancher
```

<br>

> **Note:** When using a persistent console and in the current version's console, [rolling back](/docs/installation/upgrading#rolling-back-an-upgrade) is not supported. For example, rolling back to v0.4.5 when using a v0.5.0 persistent console is not supported.

### Enabling Consoles

You can also enable a console that will be changed at the next reboot.

For our example, we'll switch to the Debian console.

```
# Check the console running in System Docker
$ sudo system-docker ps
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS              PORTS               NAMES
95d548689e82        burmilla/os-docker:v0.5.0    "/usr/sbin/entry.sh /"   About an hour ago   Up About an hour                        docker
# Enable the Debian console
$ sudo ros console enable debian
Pulling console (burmilla/os-debianconsole:v0.5.0-3)...
v0.5.0-3: Pulling from burmilla/os-debianconsole
7268d8f794c4: Pull complete
a3ed95caeb02: Pull complete
21cb8a645d75: Pull complete
5ee1d288a088: Pull complete
c09f41c2bd29: Pull complete
02b48ce40553: Pull complete
38a4150e7e9c: Pull complete
Digest: sha256:5dbca5ba6c3b7ba6cd6ac75a1d054145db4b4ea140db732bfcbd06f17059c5d0
Status: Downloaded newer image for burmilla/os-debianconsole:v0.5.0-3
```

<br>

At the next reboot, BurmillaOS will be using the Debian console.
