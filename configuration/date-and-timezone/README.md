# Date and time zone

## Host system
The default console keeps time in the Coordinated Universal Time (UTC) zone and synchronizes clocks with the Network Time Protocol (NTP). The Network Time Protocol daemon (ntpd) is an operating system program that maintains the system time in synchronization with time servers using the NTP.

Changing the timezone in the default console can be for example done using a system-wide environment variable `/etc/environment`
```
write_files:
- path: /etc/environment
  content: |
    TZ="Europe/Amsterdam"
  append: true
```

BurmillaOS can run ntpd in the System Docker container. You can update its configurations by updating `/etc/ntp.conf`. For an example of how to update a file such as `/etc/ntp.conf` within a container, refer to [this page.](/configuration/write-files/#writing-files-in-specific-system-services)

## Usage in containers

The host timezone is not used within containers and therefore needs to be set as an environment variable:
```
$ docker run -e TZ=Europe/Amsterdam debian:jessie date
Tue Aug 20 09:28:19 CEST 2019
```

You may need to install the `tzdata` in some images:

```
$ docker run -e TZ=Asia/Shanghai -e DEBIAN_FRONTEND=noninteractive -it --rm ubuntu /bin/bash -c "apt-get update && apt-get install -yq tzdata && date”
Thu Aug 29 08:13:02 CST 2019
```
