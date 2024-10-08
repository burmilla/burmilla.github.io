# System Docker Volumes

A few services are containers in `created` state. Their purpose is to provide volumes for other services.

## user-volumes

Provides user accessible persistent storage directories, used by console service:

```shell
/home
/opt
/var/lib/kubelet
```

If you want to change user-volumes, for example, add `/etc/kubernetes` directory:

```shell
$ sudo ros config set rancher.services.user-volumes.volumes  [/home:/home,/opt:/opt,/var/lib/kubelet:/var/lib/kubelet,/etc/kubernetes:/etc/kubernetes]
$ sudo reboot
```

Please note that after the restart, the new persistence directory can take effect.

## container-data-volumes

Provides docker storage directory, used by console service (and, indirectly, by docker)

```shell
/var/lib/docker
```

## command-volumes

Provides necessary command binaries (read-only), used by system services:

```shell
/usr/bin/docker-containerd.dist
/usr/bin/docker-containerd-shim.dist
/usr/bin/docker-runc.dist
/usr/bin/docker.dist
/usr/bin/dockerlaunch
/usr/bin/system-docker
/sbin/poweroff
/sbin/reboot
/sbin/halt
/sbin/shutdown
/usr/bin/respawn
/usr/bin/ros
/usr/bin/cloud-init
/usr/sbin/netconf
/usr/sbin/wait-for-docker
/usr/bin/switch-console
```

## system-volumes

Provides necessary persistent directories, used by system services:

```shell
/host/dev
/etc/docker
/etc/hosts
/etc/resolv.conf
/etc/ssl/certs/ca-certificates.crt.burmilla
/etc/selinux
/lib/firmware
/lib/modules
/run
/usr/share/ros
/var/lib/rancher/cache
/var/lib/rancher/conf
/var/lib/rancher
/var/log
/var/run
```

## all-volumes

Combines all of the above, used by the console service.
