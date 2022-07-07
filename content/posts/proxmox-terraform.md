+++
title = "LXC, Proxmox and Terraform"
date = "2020-12-07"
author = "Mark"
cover = "/img/lonely-martian.jpg"
tags = ["proxmox", "linux", "terraform"]
keywords = ["proxmox", "linux", "terraform"]
description = "Managing lxc containers on Proxmox with Terraform"
showFullContent = false
draft = false
+++

Quick post with an example of using Terraform to create LXC containers on
Proxmox.

There are a few proxmox providers out there in the wilds, I chose to use the one
by [telmate](https://github.com/Telmate/terraform-provider-proxmox):

```bash
terraform {
  required_providers {
    proxmox = {
      source  = "Telmate/proxmox"
      version = "2.6.5"
    }
  }
}
```

## Creating LXC Containers

The Docs for this suck, here is a working snippet for version 2.6.5 of the
provider:

```bash
resource "proxmox_lxc" "lxc-test" {
  hostname = "terraform-new-container"
  cores    = 1
  memory   = "1024"
  swap     = "2048"
  features {
    nesting = true
  }
  rootfs {
    storage = "local-lvm"
    size    = "8G"
  }
  network {
    name   = "eth0"
    bridge = "vmbr0"
    ip     = "dhcp"
    ip6    = "dhcp"
  }
  ostemplate   = "local:vztmpl/ubuntu-20.04-standard_20.04-1_amd64.tar.gz"
  password     = "super-secret-password"
  target_node  = "pve"
  unprivileged = true
  onboot       = true
  start        = false
}
```

For more information on the Proxmox API have a look at the
[docs](https://pve.proxmox.com/pve-docs/api-viewer/index.html#/nodes/{node}/lxc)
and the Terraform Provider [source
code](https://github.com/Telmate/terraform-provider-proxmox/blob/master/proxmox/resource_lxc.go).
