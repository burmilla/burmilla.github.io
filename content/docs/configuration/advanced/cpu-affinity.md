---
title: CPU Affinity for User Containers
weight: 1
bookToc: false
---
# CPU Affinity for User Containers

Virtual machines under heavy load can drop network connections, and in a Docker Swarm the node can be reported as down. A common cause is that the user containers consume all CPU time so the operating system, System Docker and the Docker engine do not get scheduled in time.

One way to prevent this is to keep the first CPU free for the system and let user containers run only on the remaining CPUs. This is mostly useful on private virtualization platforms where adding a vCPU to a VM is cheap.

The Docker engine creates the cgroup of every user container below the `docker` cgroup. Setting `cpuset.cpus` on that parent cgroup restricts all user containers at once, and containers started later inherit the limit. The cgroup layout differs between BurmillaOS versions.

## BurmillaOS v2.0.x (cgroup v1)

```yaml
#cloud-config
write_files:
- path: /etc/rc.local
  permissions: "0755"
  owner: root
  content: |
    #!/bin/bash
    cpuCount=$(grep -c ^processor /proc/cpuinfo)
    firstCPU=1
    lastCPU=$((cpuCount - 1))
    mkdir -p /sys/fs/cgroup/cpuset/docker
    echo "${firstCPU}-${lastCPU}" > /sys/fs/cgroup/cpuset/docker/cpuset.cpus
    exit 0
```

## BurmillaOS v3.0.x (cgroup v2)

BurmillaOS v3 uses the cgroup v2 unified hierarchy, so there is a single `/sys/fs/cgroup/docker` cgroup and the `cpuset` controller has to be enabled for it first:

```yaml
#cloud-config
write_files:
- path: /etc/rc.local
  permissions: "0755"
  owner: root
  content: |
    #!/bin/bash
    cpuCount=$(grep -c ^processor /proc/cpuinfo)
    firstCPU=1
    lastCPU=$((cpuCount - 1))
    echo "+cpuset" > /sys/fs/cgroup/cgroup.subtree_control
    mkdir -p /sys/fs/cgroup/docker
    echo "${firstCPU}-${lastCPU}" > /sys/fs/cgroup/docker/cpuset.cpus
    exit 0
```

> **Note:** The v3 variant follows the cgroup v2 documentation and has not been verified on every v3 test release. After a reboot check that `cat /sys/fs/cgroup/docker/cpuset.cpus.effective` prints the expected range before relying on it.

## Verifying

Start a CPU stress test in the user Docker:

```bash
$ docker run -d --name cpustress --rm containerstack/cpustress --cpu 8 --timeout 1m --metrics-brief
```

Then run `top` and press `1` to show the per-CPU view. The first CPU should stay (almost) idle while the others are at 100 %.

> **Note:** On a VM with a single vCPU this configuration leaves no CPU for the user containers. Only use it on machines with two or more CPUs.
