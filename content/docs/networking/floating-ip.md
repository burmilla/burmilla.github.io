---
title: Floating IP
bookToc: true
---
# Floating IP with keepalived

_Available since BurmillaOS v3.0._

The optional `keepalived` system service runs
[keepalived](https://www.keepalived.org/) in System Docker, so that a floating
IP (VRRP virtual IP) moves between the nodes of a cluster. Typical uses are a
stable address in front of Docker Swarm managers, or in front of the nodes
that publish ingress ports.

keepalived needs node specific settings (priority, own address), so the
service is configured with `KEEPALIVED_*` variables in `rancher.environment`.
Every BurmillaOS node has its own cloud-config, so each node gets its own
values and the configuration file is generated when the service starts. The
full list of variables is in the
[service README](https://github.com/burmilla/os-services/blob/v3.0.x/images/20-keepalived/README.md).

## Enable

```bash
sudo ros config set rancher.environment.KEEPALIVED_VIRTUAL_IPS 192.168.1.100/24
sudo ros config set rancher.environment.KEEPALIVED_PRIORITY 150
sudo ros service enable keepalived
sudo ros service up keepalived
```

or with cloud-init:

```yaml
#cloud-config
rancher:
  environment:
    KEEPALIVED_VIRTUAL_IPS: 192.168.1.100/24
    KEEPALIVED_PRIORITY: "150"
    KEEPALIVED_PASSWORD: s3cret
  services_include:
    keepalived: true
```

Use the same `KEEPALIVED_VIRTUAL_IPS`, `KEEPALIVED_PASSWORD` and
`KEEPALIVED_ROUTER_ID` (default `51`) on every node, and a different
`KEEPALIVED_PRIORITY`. The node with the highest priority owns the floating
IP. The interface defaults to the one with the default route; set
`KEEPALIVED_INTERFACE` to use another one.

Check which node is the master with `ip addr show` (the floating IP is
listed on the master) and the service log with
`sudo system-docker logs keepalived`.

## Health check

With `KEEPALIVED_CHECK_SCRIPT` the node gives the floating IP away while the
command fails. For example, to keep the address on a reachable Swarm
manager:

```yaml
#cloud-config
rancher:
  environment:
    KEEPALIVED_VIRTUAL_IPS: 192.168.1.100/24
    KEEPALIVED_PRIORITY: "150"
    KEEPALIVED_CHECK_SCRIPT: nc -z 127.0.0.1 2377
  services_include:
    keepalived: true
```

## Unicast

VRRP uses multicast by default, which most clouds and some switches block.
List the other nodes in `KEEPALIVED_UNICAST_PEERS` to use unicast instead:

```yaml
#cloud-config
rancher:
  environment:
    KEEPALIVED_VIRTUAL_IPS: 192.168.1.100/24
    KEEPALIVED_UNICAST_PEERS: 192.168.1.12, 192.168.1.13
  services_include:
    keepalived: true
```

## Own configuration file

For several VRRP instances, IPv6, virtual servers (IPVS) or notify scripts,
write a complete `keepalived.conf` to `/var/lib/rancher/conf/keepalived.conf`
with [`write_files`](/docs/configuration/advanced/write-files/). When the file
exists, the `KEEPALIVED_*` variables are ignored.

## Notes

* Applications that bind to the floating IP on a node which is not the
  master need `net.ipv4.ip_nonlocal_bind=1`:

  ```yaml
  rancher:
    sysctl:
      net.ipv4.ip_nonlocal_bind: "1"
  ```

* In Docker Swarm the floating IP can also be published by a service
  running keepalived as a user container (with `cap_add: [NET_ADMIN,
  NET_BROADCAST, NET_RAW]` and host networking). The system service is the
  simpler choice when the address must not depend on User Docker, and
  [docker-bgp-lb](https://github.com/olljanat/docker-bgp-lb) is an
  alternative for networks with BGP.
