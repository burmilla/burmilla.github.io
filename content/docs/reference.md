# Reference

This page lists the `ros` commands and the keys which can be used in [cloud-config](/docs/configuration/base/#cloud-config) files and with `ros config set`. `ros <command> -h` prints the help of every command.

## `ros` Commands

| Commands / Options          | Description                            | Example                                |
|---------------------------|----------------------------------------|----------------------------------------|
| &nbsp; `--version` / `-v` | print the version                      | `ros -v`                               |
| &nbsp; `--help` / `-h`    | show help                              | `ros -h`                               |
||||
| `os`                      | operating system upgrade/downgrade     | [see below](#ros-os-sub-commands)      |
| `config`                  | configure settings                     | [see below](#ros-config-sub-commands)  |
| `console`                 | manage which console container is used | [see below](#ros-console-sub-commands) |
| `install`                 | install BurmillaOS to disk             | [see below](#ros-install-sub-command)  |
| `engine`                  | manage which Docker engine is used     | [see below](#ros-engine-sub-commands)  |
| `service`                 | manage system services                 | [see below](#ros-service-sub-commands) |
| `tls`                     | setup tls configuration                | [see below](#ros-tls-sub-command)      |

### `ros os` Sub-Commands

| Commands / Options         | Description                                | Example                            |
|----------------------------|--------------------------------------------|------------------------------------|
| `version`                  | show the currently installed version       | `ros os version`                   |
||||
| `list`                     | list the current available versions        | `ros os list`                      |
| &nbsp; `--update` / `-u`   | update engine cache                        | `ros os list --update`             |
||||
| `upgrade`                  | upgrade to the latest version              | `ros os upgrade`                   |
| &nbsp; `--image` / `-i`    | upgrade to a certain image                 | `ros os upgrade --image value`     |
| &nbsp; `--stage` / `-s`    | only stage the new upgrade, don't apply it | `ros os upgrade --stage`           |
| &nbsp; `--force` / `-f`    | do not prompt for input                    | `ros os upgrade --force`           |
| &nbsp; `--kexec` / `-k`    | reboot using kexec                         | `ros os upgrade --kexec`           |
| &nbsp; `--no-reboot`       | do not reboot after update                 | `ros os upgrade --no-reboot`       |
| &nbsp; `--append`          | append additional kernel parameters        | `ros os upgrade --append value`    |
| &nbsp; `--upgrade-console` | upgrade console even if persistent         | `ros os upgrade --upgrade-console` |
| &nbsp; `--debug`           | run installer with debug output            | `ros os upgrade --debug`           |

### `ros config` Sub-Commands

| Commands / Options        | Description                                                        | Example                             |
|---------------------------|--------------------------------------------------------------------|-------------------------------------|
| `get`                     | get value                                                          | `ros config get value`              |
||||
| `set`                     | set a value                                                        | `ros config set value`              |
||||
| `images`                  | list Docker images for a configuration from a file                 | `ros config images`                 |
| &nbsp; `--input` / `-i`   | file from which to read config                                     | `ros config images --input value`   |
||||
| `generate`                | generate a configuration file from a template                      | `ros config generate`               |
||||
| `merge`                   | merge configuration from stdin                                     | `ros config merge`                  |
| &nbsp; `--input` / `-i`   | file from which to read                                            | `ros config merge --input value`    |
||||
| `export`                  | export configuration                                               | `ros config export`                 |
| &nbsp; `--output` / `-o`  | file to which to save                                              | `ros config export --output value`  |
| &nbsp; `--private` / `-p` | include the generated private keys                                 | `ros config export --private`       |
| &nbsp; `--full` / `-f`    | export full configuration, including internal and default settings | `ros config export --full`          |
||||
| `validate`                | validate configuration form stdin                                  | `ros config validate`               |
| &nbsp; `--input` / `-i`   | file from which to read                                            | `ros config validate --input value` |
||||
| `syslinux`                | edit Syslinux boot global.cfg                                      | `ros config syslinux`               |

### `ros console` Sub-Commands

| Commands / Options       | Description                             | Example                                |
|--------------------------|-----------------------------------------|----------------------------------------|
| `list`                   | list available consoles                 | `ros console list`                     |
| &nbsp; `--update` / `-u` | update engine cache                     | `ros console list --update`            |
||||
| `enable`                 | set console to be switched on next boot | `ros console enable default`           |
||||
| `switch`                 | switch console without a reboot         | `ros console switch default`           |
| &nbsp; `--force` / `-f`  | do not prompt for input                 | `ros console switch --force default`   |
| &nbsp; `--no-pull`       | don't pull console image                | `ros console switch --no-pull default` |
||||
| `sshd status`            | print whether sshd is enabled or disabled | `ros console sshd status`            |
| `sshd disable`           | stop sshd until enabled again or reboot | `ros console sshd disable`             |
| &nbsp; `--if-idle`       | only when no SSH connection is established | `ros console sshd disable --if-idle` |
| `sshd enable`            | start sshd again                        | `ros console sshd enable`              |
||||
| `access status`          | print whether interactive access (sshd and tty logins) is enabled | `ros console access status` |
| `access disable`         | stop sshd and tty logins, also after reboots (`rancher.console_access: false`) | `ros console access disable` |
| `access enable`          | start sshd and tty logins again         | `ros console access enable`            |

#### Available Consoles

| Console   | BurmillaOS | Base image            | Description          |
|-----------|------------|-----------------------|----------------------|
| `default` | v3.0.x     | `debian:trixie-slim`  | Debian 13 "Trixie"   |
| `default` | v2.0.x     | `debian:bullseye-slim`| Debian 11 "Bullseye" |

`default` is the only officially supported console. Other consoles (Ubuntu, Alpine, Fedora, ...) are available as [unofficial custom console images](/docs/installation/custom-builds/custom-console/#using-the-unofficial-custom-console-images).

### `ros install` Sub-Command

| Command / Options              | Description                                                              | Example                            |
|--------------------------------|--------------------------------------------------------------------------|------------------------------------|
| `install`                      | Install BurmillaOS to disk                                               | `ros install`                      |
| &nbsp; `--cloud-config` / `-c` | cloud-config yml file - needed for SSH authorized keys                   | `ros install --cloud-config value` |
| &nbsp; `--device` / `-d`       | storage device                                                           | `ros install --device value`       |
| &nbsp; `--image` / `-i`        | install from a certain image                                             | `ros install --image value`        |
| &nbsp; `--save` / `-s`         | save services and images for next booting                                | `ros install --save`               |
| &nbsp; `--install-type` / `-t` | generic (default; BIOS or UEFI layout is selected automatically on v3), amazon-ebs-hvm, googlecompute, noformat, raid | `ros install --install-type value` |
| &nbsp; `--force` / `-f`        | **[DANGEROUS! Data loss can happen]** partition/format without prompting | `ros install --force`              |
| &nbsp; `--partition` / `-p`    | partition to install to                                                  | `ros install --partition value`    |
| &nbsp; `--append` / `-a`       | append additional kernel parameters                                      | `ros install --append value`       |
| &nbsp; `--debug`               | run installer with debug output                                          | `ros install --debug`              |
| &nbsp; `--statedir value`      | install to `rancher.state.directory`                                     | `ros install --statedir value`     |
| &nbsp; `--kexec` / `-k`        | reboot using kexec                                                       | `ros install --kexec`              |
| &nbsp; `--no-reboot`           | do not reboot after install                                              | `ros install --no-reboot`          |
| &nbsp; `--rollback` / `-r`     | rollback version                                                         | `ros install --rollback`           |       

### `ros engine` Sub-Commands

| Commands / Options         | Description                                              | Example                                       |
|----------------------------|----------------------------------------------------------|-----------------------------------------------|
| `list`                     | list available Docker engines (include the DinD engines) | `ros engine list`                             |
| &nbsp; `--update` / `-u`   | update engine cache                                      | `ros engine list --update`                    |
||||
| `switch`                   | switch user Docker engine without reboot                 | `ros engine switch current`                   |
| &nbsp; `--force` / `-f`    | do not prompt for input                                  | `ros engine switch --force current`           |
| &nbsp; `--no-pull`         | don't pull engine image                                  | `ros engine switch --no-pull current`         |
||||
| `enable`                   | set user Docker engine to be switched on next boot       | `ros engine enable current`                   |
||||
| `create`                   | create DinD engine without a reboot                      | `ros engine create my_name`                   |
| &nbsp; `--version` / `-v`  | set version for the engine                               | `ros engine create --version value my_name`   |
| &nbsp; `--network`         | set the network for the engine                           | `ros engine create --network value my_name`   |
| &nbsp; `--fixed-ip`        | set the fixed ip for the engine                          | `ros engine create --fixed-ip value my_name`  |
| &nbsp; `--ssh-port`        | set the ssh port for the engine                          | `ros engine create --ssh-port value my_name`  |
| &nbsp; `--authorized-keys` | set the authorized_keys absolute path for the engine     | `ros engine create --authorized-keys my_name` |
||||
| `rm`                       | remove DinD engine without a reboot                      | `ros engine rm my_name`                       |
| &nbsp; `--force` / `-f`    | do not prompt for input                                  | `ros engine rm --force my_name`               |
| &nbsp; `--timeout` / `-t`  | specify a shutdown timeout in seconds (default: 10)      | `ros engine rm --timout 10 my_name`           |

### `ros service` Sub-Commands

| Commands / Options              | Description                                                    | Example                                     |
|---------------------------------|----------------------------------------------------------------|---------------------------------------------|
| `list`                          | list services and state                                        | `ros service list`                          |
| &nbsp; `--update` / `-u`        | update service cache                                           | `ros service list --update`                 |
| &nbsp; `--all` / `-a`           | list all services and state                                    | `ros service list --all`                    |
||||
| `up`                            | create and start containers                                    | `ros service up`                            |
| &nbsp; `--foreground`           | run in foreground and log                                                                                         | `ros service up --foreground`     |
| &nbsp; `--no-build`             | don't build an image, even if it's missing                                                                        | `ros service up --no-build`       |
| &nbsp; `--no-recreate`          | if containers already exists, don't recreate them. <br/> Incompatible with `--force-recreate`                     | `ros service up --no-recreate`    |
| &nbsp; `--force-recreate`       | recreate containers even if their configuration and imager haven't changed. <br/> Incompatible with `no-recreate` | `ros service up --force-recreate` |
||||
| `down`                          | stop and remove containers, networks, images, and volumes      | `ros service down`                          |
| &nbsp; `--volumes` / `-v`       | remove data volume | `ros service down --volumes`
| &nbsp; `--rmi`                  | remove images, type may be one of: <br/> 'all' to remove all images, or <br/> 'local' to remove only images that don't have an custom name set by the `image` field | `ros service down --rmi value` |
| &nbsp; `--remove-orphans`       | remove containers for services not defined in the Compose file | `ros service down --remove orphans`         |
||||
| `enable`                        | turn on a service                                              | `ros service enable`                        |
||||
| `disable`                       | turn off a service                                             | `ros service disable`                       |
||||
| `rm`                            | delete services                                                | `ros service rm`                            |
| &nbsp; `--force` / `-f`         | allow deletion of all services                                 | `ros service rm --force`                    |
| &nbsp; `-v`                     | remove volumes associated with containers                      | `ros service rm -v`                         |
||||
| `delete`                        | delete a service                                               | `ros service delete`                        |
||||
| `logs`                          | view output from containers                                    | `ros service logs`                          |
| &nbsp; `--lines`                | number of lines to tail (default: 100)                         | `ros service logs --lines`                  |
| &nbsp; `--follow` / `-f`        | follow log output                                              | `ros service logs --follow`                 |
||||
| `build`                         | build or rebuild services                                      | `ros service build`                         |
| &nbsp; `--no-cache`             | do not use cache when building the image                       | `ros service build --no cache`              |
| &nbsp; `--force-rm`             | always remove intermediate containers                          | `ros service build --force-rm`              |
| &nbsp; `--pull`                 | alwys attempt to pull a newer version of the image             | `ros service build --pull`                  |
||||
| `create`                        | create services                                                | `ros service create`                        |
| &nbsp; `--no-recreate`          | if containers already exist, don't recreate them. <br/> Incompatible with `--force-recreate` | `ros service create --no-recreate` |
| &nbsp; `--force-recreate`       | recreate containers even if their configuration and images haven't changed. <br/> Incompatible with `--no-recreate` | `ros service create --force-recreate` |
| &nbsp; `--no-build`             | don't build an image, even if it is missing                    | `ros service create --no-build`             |
||||
| `start`                         | start services                                                 | `ros service start`                         |
| &nbsp; `--foreground`           | run in foreground and log                                      | `ros service start --foreground`            |
||||
| `restart`                       | restart services                                               | `ros service restart`                       |
| &nbsp; `--timeout` / `-t`       | specify a shutdown timeout in seconds (default: 10)            | `ros service restart --timeout 10`          |
||||
| `stop`                          | stop services                                                  | `ros service stop`                          |
| &nbsp; `--timeout` / `-t`       | specify a shutdown timeout in seconds (default: 10)            | `ros service stop --timeout 10`             |
||||
| `kill`                          | kill containers                                                | `ros service kill`                          |
| &nbsp; `--signal` / `-s`        | SIGAL to send to the container (default: "SiGKILL")            | `ros service kill --signal value`           |
||||
| `pull`                          | pulls service images                                           | `ros service pull`                          |
| &nbsp; `--ignore-pull-failures` | pull what it can and ignores images with pull failures         | `ros service pull ----ignore-pull-failures` |
||||
| `ps`                            | list containers                                                | `ros service ps`                            |
| &nbsp; `-q`                     | only display IDs                                               | `ros service ps -q`                         |

### `ros tls` Sub-Command

| Command / Options          | Description                                                                      | Example                            |
|----------------------------|----------------------------------------------------------------------------------|------------------------------------|
| `generate` / `gen`         | generates new set of TLS configurations certs                                    | `ros tls gen`                      |
| &nbsp; `--hostname` / `-H` | the hostname for which you want to generate the certificate (default: localhost) | `ros tls gen --hostname localhost` |
| &nbsp; `--server` / `-s`   | generate the server keys instead of client keys                                  | `ros tls gen --server`             |
| &nbsp; `--dir` / `-d`      | the directory to save/read certs from/to                                         | `ros tls gen --dir value`          |


## Cloud-Config and `ros config` Keys

The same keys are used in the cloud-config file given at installation or first boot and with `ros config set <key> <value>` / `ros config merge` later on, see [Configuration](/docs/configuration/base/). The defaults come from the [os-config.tpl.yml](https://github.com/burmilla/os/blob/master/os-config.tpl.yml) template of the BurmillaOS version you run. `sudo ros config export --full` prints the complete effective configuration of your installation including all defaults, which is the quickest way to see the current value and the exact spelling of a key.

### Top-Level Keys

| Key                   | Type                    | Description                                                                                                                      |
|-----------------------|-------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| `hostname`            | string                  | [Hostname](/docs/configuration/base/hostname) of the system                                                                      |
| `ssh_authorized_keys` | list of strings         | Public keys added to the `rancher` user, see [SSH keys](/docs/configuration/base/ssh-keys)                                       |
| `write_files`         | list of files           | Files to write on boot (`path`, `content`, `permissions`, `owner`, `encoding`), see [Writing files](/docs/configuration/advanced/write-files) |
| `runcmd`              | list of commands        | Commands run in the console on boot, before Docker starts, see [Running commands](/docs/configuration/advanced/running-commands) |
| `bootcmd`             | list of commands        | Commands run early in the boot process, before the console starts                                                               |
| `mounts`              | list of `[device, path, fstype, options]` | [Additional mounts](/docs/storage/additional-mounts)                                                                   |
| `rancher`             | map                     | BurmillaOS specific settings, see below                                                                                          |

Other cloud-init directives such as `users` are not supported, see [Users](/docs/configuration/base/users).

### `rancher` Keys

| Key                             | Type              | Description                                                                                                                                                     |
|---------------------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `rancher.console`               | string            | Console to use, see [Switching consoles](/docs/configuration/base/switching-consoles)                                                                           |
| `rancher.docker`                | map               | User Docker settings, see below                                                                                                                                 |
| `rancher.system_docker`         | map               | System Docker settings, same keys as `rancher.docker`                                                                                                           |
| `rancher.bootstrap_docker`      | map               | Settings of the Docker used during the bootstrap phase of the boot, same keys as `rancher.docker`                                                               |
| `rancher.environment`           | map               | Environment variables passed to system services, e.g. `HTTP_PROXY`, see [Environment](/docs/system-services/environment) and [Proxy settings](/docs/networking/proxy-settings) |
| `rancher.services`              | map               | [Custom system services](/docs/system-services/custom-system-services) and overrides for built-in services, in Docker Compose v1 format                       |
| `rancher.services_include`      | map of booleans   | Optional [system services](/docs/system-services/) from the service repository to enable, e.g. `open-vm-tools: true`                                           |
| `rancher.bootstrap`             | map               | Services run during the bootstrap phase (before the state partition is mounted)                                                                                |
| `rancher.cloud_init_services`   | map               | Services run while cloud-init fetches the configuration                                                                                                        |
| `rancher.cloud_init.datasources`| list of strings   | Where to fetch user data and metadata from, e.g. `configdrive:/media/config-2`, `ec2`, `gce`, `digitalocean`, `vmware`, `azure`, `proxmox`, `url:<URL>`, `file:<path>`, see [Cloud init](/docs/installation/boot-process/cloud-init) |
| `rancher.network`               | map               | Interfaces, DNS, proxy and other [networking](/docs/networking/) settings                                                                                       |
| `rancher.ssh.keys`              | map               | Host keys of the sshd in the console (generated on first boot, exported with `ros config export --private`)                                                   |
| `rancher.ssh.daemon`            | boolean           | Run sshd in the console (default `true`)                                                                                                                       |
| `rancher.ssh.port`              | integer           | Port sshd listens on                                                                                                                                           |
| `rancher.ssh.listen_address`    | string            | Address sshd listens on                                                                                                                                        |
| `rancher.state`                 | map               | [State partition](/docs/storage/state-partition) settings: `dev`, `fstype`, `directory`, `wait`, `required`, `autoformat`, `mdadm_scan`, `lvm_scan`, `cryptsetup`, `rngd`, `script`, `oem_dev`, `oem_fstype`, `boot_dev`, `boot_fstype` |
| `rancher.upgrade.url`           | string            | URL of the `releases.yml` used by `ros os list` / `ros os upgrade`, see [Upgrading](/docs/installation/upgrading#custom-upgrade-sources)                     |
| `rancher.upgrade.image`         | string            | Image used for upgrades (default `burmilla/os`)                                                                                                                |
| `rancher.repositories`          | map               | Service repositories (`core` is the BurmillaOS [os-services](https://github.com/burmilla/os-services) repository), see [Air gap configuration](/docs/configuration/advanced/airgap-configuration) |
| `rancher.registry_auths`        | map               | Credentials for [private registries](/docs/configuration/docker/private-registries) used by System Docker                                                     |
| `rancher.modules`               | list of strings   | Extra kernel modules to load on boot, see [Loading kernel modules](/docs/configuration/kernel/loading-kernel-modules)                                          |
| `rancher.sysctl`                | map               | Kernel parameters, see [Sysctl settings](/docs/configuration/advanced/sysctl)                                                                                   |
| `rancher.resize_device`         | string            | Device whose first partition is grown to the full disk on boot, see [Resizing a device partition](/docs/configuration/advanced/resizing-device-partition)      |
| `rancher.disable`               | list of strings   | Console features to disable: `password` (password login) and `autologin` (automatic login on the local ttys), see [Disabling access](/docs/configuration/advanced/disable-access-to-system)                                                                                  |
| `rancher.restart_services`      | list of strings   | System services to restart after cloud-init has applied the configuration                                                                                      |
| `rancher.hypervisor_service`    | boolean           | Automatically enable the `open-vm-tools` or `hyperv-vm-tools` service when the matching hypervisor is detected (default `true`)  |
| `rancher.shutdown_timeout`      | integer           | Seconds to wait for containers to stop on shutdown                                                                                                              |
| `rancher.http_load_retries`     | integer           | Retries when fetching the cloud-config or service definitions over HTTP                                                                                        |
| `rancher.preload_wait`          | boolean           | Wait for [image preloading](/docs/installation/boot-process/image-preloading) to finish before starting Docker                                                 |
| `rancher.force_console_rebuild` | boolean           | Recreate the (persistent) console container on boot                                                                                                            |
| `rancher.debug`                 | boolean           | Enable debug logging of `ros`                                                                                                                                  |
| `rancher.log`                   | boolean           | Stream the output of system services to the console log while they start, see [Logging](/docs/installation/boot-process/logging)                                                                 |
| `rancher.recovery`              | boolean           | Boot the [recovery console](/docs/additional-resources/recovery-console)                                                                                       |
| `rancher.defaults`              | map               | Defaults for `hostname`, `docker`, `network` and `system_docker_logs`, meant for OEM configuration                                                              |

Keys such as `rancher.rm_usr` and `rancher.no_sharedroot` exist for compatibility with RancherOS and should not be changed.

### `rancher.docker` Keys

These keys configure the user Docker engine. The same keys exist under `rancher.system_docker` and `rancher.bootstrap_docker`. See [Docker configuration](/docs/configuration/docker/) for examples.

| Key                                   | Type            | Description                                                                                                        |
|---------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------|
| `rancher.docker.engine`               | string          | Docker engine to run, e.g. `docker-29.2.1` or `latest`, see [Switching Docker versions](/docs/configuration/docker/switching-docker-versions) |
| `rancher.docker.extra_args`           | list of strings | Additional `dockerd` command line arguments, e.g. `["--default-ulimit", "nofile=65536:65536"]`                    |
| `rancher.docker.environment`          | list of strings | Environment variables for the Docker daemon, e.g. `["HTTP_PROXY=http://proxy:3128"]`                              |
| `rancher.docker.tls`                  | boolean         | Enable TLS on the Docker API, see [Setting up Docker TLS](/docs/configuration/docker/setting-up-docker-tls)        |
| `rancher.docker.tls_args`             | list of strings | `dockerd` arguments used when `tls` is enabled                                                                     |
| `rancher.docker.server_cert`, `server_key`, `ca_cert`, `ca_key` | string | TLS certificates and keys (set by `ros tls generate`)                                              |
| `rancher.docker.host`                 | list of strings | Sockets / addresses the daemon listens on (`-H`)                                                                   |
| `rancher.docker.group`                | string          | Group owning the Docker socket (default `docker`)                                                                  |
| `rancher.docker.storage_driver`       | string          | Storage driver, e.g. `overlay2`                                                                                    |
| `rancher.docker.data_root`            | string          | Data directory of the daemon (default `/var/lib/docker`)                                                           |
| `rancher.docker.exec_root`            | string          | Exec root directory of the daemon                                                                                  |
| `rancher.docker.pid_file`             | string          | PID file of the daemon                                                                                             |
| `rancher.docker.config_file`          | string          | `daemon.json` to load                                                                                              |
| `rancher.docker.log_driver`           | string          | Default container log driver                                                                                       |
| `rancher.docker.log_opts`             | map             | Default container log options, e.g. `max-size: 25m`                                                                |
| `rancher.docker.insecure_registry`    | list of strings | Registries to allow over plain HTTP                                                                                |
| `rancher.docker.registry_mirror`      | string          | Registry mirror URL                                                                                                |
| `rancher.docker.bridge`, `bip`        | string          | Bridge name and IP range of the default bridge network                                                             |
| `rancher.docker.live_restore`         | boolean         | Keep containers running while the daemon restarts                                                                  |
| `rancher.docker.userland_proxy`       | boolean         | Use the userland proxy for port forwarding                                                                         |
| `rancher.docker.debug`                | boolean         | Daemon debug logging                                                                                               |

Boolean and list values are given to `ros config set` in YAML syntax, e.g. `sudo ros config set rancher.docker.extra_args "['--default-ulimit','nofile=65536:65536']"`. A reboot (or `sudo system-docker restart docker` for `rancher.docker` keys) is needed before changed values are applied.
