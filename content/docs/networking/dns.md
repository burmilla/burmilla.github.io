---
title: DNS
bookToc: false
---
# Configuring DNS

If you wanted to configure the DNS through the cloud config file, you'll need to place DNS configurations within the `burmilla` key.

```yaml
#cloud-config

#Remember, any changes for burmilla will be within the burmilla key
rancher:
  network:
    dns:
      search:
        - mydomain.com
        - example.com
```

Using `ros config`, you can set the `nameservers`, and `search`, which directly map to the fields of the same name in `/etc/resolv.conf`.

```bash
$ sudo ros config set burmilla.network.dns.search "['mydomain.com','example.com']"
$ sudo ros config get burmilla.network.dns.search
- mydomain.com
- example.com
```
