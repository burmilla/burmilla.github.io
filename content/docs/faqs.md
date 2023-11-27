---
title: FAQs
weight: 11
---

# FAQs

## What is required to run BurmillaOS?

BurmillaOS runs on any laptop, physical, or virtual servers.

## What are some commands?

Command | Description
--------|------------
`docker`| Good old Docker, use that to run stuff.
`system-docker` | The Docker instance running the system containers.  Must run as root or using `sudo`
`ros` | Control and configure BurmillaOS


## How can I extend my disk size in Amazon?

Assuming your EC2 instance with BurmillaOS with more disk space than what's being read, run the following command to extend the disk size. This allows BurmillaOS to see the disk size.

```
$ docker run --privileged --rm --it debian:jessie resize2fs /dev/xvda1
```

`xvda1` should be the right disk for your own setup. In the future, we will be trying to create a system service that would automatically do this on boot in AWS.

## Why the name BurmillaOS?

The "Rancher" in BurmillaOS's predecessor RancherOS came from the [Pets vs Cattle](https://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/) analogy.
While RancherOS was founded on the "cattle" approach, actually, servers often enough end up being pets.
Thus, the name [Burmilla](https://en.wikipedia.org/wiki/Burmilla), a breed of pet cats, was chosen.
