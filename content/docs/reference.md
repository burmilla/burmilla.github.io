# Reference

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
| `service`                 |                                        | [see below](#ros-service-sub-commands) |
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

#### Available Consoles

| Console          | Image                | Description                                        |
|------------------|----------------------|----------------------------------------------------|
| `default`        | `debian:buster-slim` | Debian 10 "Buster" (slimmer base)                  |
| `debian`         | `debian:buster`      | Debian 10 "Buster"                                 |
| `debian_testing` | `debian:testing`     | Debian 10 "Buster" (more recent software)          |
| `ubuntu`         | `ubuntu:latest`      | [Ubuntu "latest"](https://hub.docker.com/_/ubuntu) |
| `alpine`         | `alpine:latest`      | [Alpine "latest"](https://hub.docker.com/_/alpine) |
| `fedora`         | `fedora:latest`      | [Fedora "latest"](https://hub.docker.com/_/fedora) |

### `ros install` Sub-Command

| Command / Options              | Description                                                              | Example                            |
|--------------------------------|--------------------------------------------------------------------------|------------------------------------|
| `install`                      | Install BurmillaOS to disk                                               | `ros install`                      |
| &nbsp; `--cloud-config` / `-c` | cloud-config yml file - needed for SSH authorized keys                   | `ros install --cloud-config value` |
| &nbsp; `--device` / `-d`       | storage device                                                           | `ros install --device value`       |
| &nbsp; `--image` / `-i`        | install from a certain image                                             | `ros install --image value`        |
| &nbsp; `--save` / `-s`         | save services and images for next booting                                | `ros install --save`               |
| &nbsp; `--install-type` / `-t` | generic (default), amazon-ebs, gptsyslinux                               | `ros install --install-type value` |
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
