# Environment

The [environment key](https://docs.docker.com/compose/compose-file/#environment) can be used to customize system services. When a value is not assigned, BurmillaOS looks up the value from the `burmilla.environment` key.

In the example below, `ETCD_DISCOVERY` will be set to `https://discovery.etcd.io/d1cd18f5ee1c1e2223aed6a1734719f7` for the `etcd` service.

```yaml
burmilla:
  environment:
    ETCD_DISCOVERY: https://discovery.etcd.io/d1cd18f5ee1c1e2223aed6a1734719f7
  services:
    etcd:
      ...
      environment:
      - ETCD_DISCOVERY
```

Wildcard globbing is also supported. In the example below, `ETCD_DISCOVERY` will be set as in the previous example, along with any other environment variables beginning with `ETCD_`.

```yaml
burmilla:
  environment:
    ETCD_DISCOVERY: https://discovery.etcd.io/d1cd18f5ee1c1e2223aed6a1734719f7
  services:
    etcd:
      ...
      environment:
      - ETCD_*
```

_Available as of v1.2_

There is also a way to extend PATH environment variable, `PATH` or `path` can be set, and multiple values can be comma-separated. Note that need to reboot before taking effect.

```yaml
burmilla:
  environment:
    path: /opt/bin,/home/burmilla/bin
```
