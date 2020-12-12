---
title: Running Commands
weight: 1
bookToc: false
---
# Running Commands

You can automate running commands on boot using the `runcmd` cloud-config directive. Commands can be specified as either a list or a string. In the latter case, the command is executed with `sh`.

```yaml
#cloud-config
runcmd:
- [ touch, /home/burmilla/test1 ]
- echo "test" > /home/burmilla/test2
```

Commands specified using `runcmd` will be executed within the context of the `console` container.

## Running Docker commands

When using `runcmd`, BurmillaOS will wait for all commands to complete before starting Docker. As a result, any `docker run` command should not be placed under `runcmd`. Instead, the `/etc/rc.local` script can be used. BurmillaOS will not wait for commands in this script to complete, so you can use the `wait-for-docker` command to ensure that the Docker daemon is running before performing any `docker run` commands.

```yaml
#cloud-config
burmilla:
write_files:
  - path: /etc/rc.local
    permissions: "0755"
    owner: root
    content: |
      #!/bin/bash
      wait-for-docker
      docker run -d nginx
```

Running Docker commands in this manner is useful when pieces of the `docker run` command are dynamically generated. For services whose configuration is static, [adding a system service](/docs/system-services/) is recommended.
