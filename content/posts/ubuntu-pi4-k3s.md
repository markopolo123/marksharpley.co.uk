+++
title = "k3s, Ubuntu 19.10 and a Pi4"
date = "2020-01-13"
author = "Mark"
cover = "/img/trees.jpg"
tags = ["k8s", "Pi4", "k3s", "ubuntu"]
keywords = ["kubernetes", "Linux"]
description = "Making Kubernetes work on a Raspberry Pi4 with Ubuntu"
+++

# On the matter of Operating Systems

Raspian is perfectly good, but there are a few niggles. It isn't 64 bit, so running things like mongodb is a problem. Also, it'd be nice to use [cloud-init](https://cloud-init.io/) to preconfigure some stuff, such as SSH keys.
If you want to try a 64 bit OS for your Pi4, Ubuntu is a solid option.

# cgroup_memory = 0

`cgroup_memory` is disabled by default on raspbian and is required for k8s.

On raspbian, cgroup_memory can be enabled by appending `cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1` to `/boot/cmdline.txt`. On Ubuntu, the location is different and may be divined by inspecting `/boot/firmware/README`.

```bash
echo -n " cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1" >> /boot/firmware/nobtcmd.txt
```

After a reboot try `cat /proc/cmdline` to make sure cgroup_memory is enabled. 
