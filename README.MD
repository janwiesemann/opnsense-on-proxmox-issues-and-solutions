<!-- I can recommend VS-Code, if you want to edit his file. If you clone this repo and use VS-Code, some plugins are recommended and a few useful settings are changed. -->

# Introduction

While virtualizing my home installation of OPNsense I've been running into a few issues.

Since nobody wants to spend a lot of there precious time with problem solving, I want to start a small collection of some issues I've been experiencing and how I was able so solve or work around them.

# Content

Entries with `[ ]` are not fix or no solution / workaround is a available. Entries with `[X]` have a solution.

- [Access /  Discuss / Contribute](#access---discuss--contribute)
  - [Reddit](#reddit)
  - [GitHub](#github)
- [General](#general)
- [Issues](#issues)
  - [Basic stuff](#basic-stuff)
  - [virtio](#virtio)
    - [[ ] virtio_balloon (PVE shows high RAM-usage)](#--virtio_balloon-pve-shows-high-ram-usage)
    - [virtio_net](#virtio_net)
      - [[X] Dropping connection to ISP while backing up a PVE-Guest](#x-dropping-connection-to-isp-while-backing-up-a-pve-guest)
  - [qemu-guest-agent](#qemu-guest-agent)

# Access /  Discuss / Contribute

I'm just another guy who likes to tinker, in his free time, I don't know everything. Others might experience different issues and use other solutions. So, feel free to contribute to this.

## Reddit
> https://www.reddit.com/r/OPNsenseFirewall/comments/y30ysj/running_opnsensepfsense_on_proxmox_a_few_issues/

If you comment a solution, I'll try to add it to the post and also to the repository.

## GitHub
> https://github.com/janwiesemann/opnsense-on-proxmox-issues-and-solutions

Use issues, pull request and so on, if you want to contribute.

# General
There are a few option available, if you want to virtualize OPNsense. My setups tend to follow some basic rules:

I try not to expose any hardware devices directly to my guests. This means, I try to avoid features like pci-pass-through or bind mounting a drive to a LXC. This allows for easier backups and restores. If you use a setup like this, you can easily migrate guests to other hosts without large changes.

# Issues

## Basic stuff
FreeBSD is the OS on which pfsense and OPNsense are based up on. It has some issues with some software. A few examples are bad support for [virtio](#virtio) or [qemu-guest-agent](#qemu-guest-agent).

## virtio
Is a driver (more a collection of drivers) to allow fast access to virtualized hardware. This can enable some nice features.

### [ ] virtio_balloon (PVE shows high RAM-usage)
Ballooning RAM isn't fully supported on FreeBSD. Due to this, PVE does not display the correct RAM-usage. FreeBSD will happily take RAM but will mostly not release it or this process will be unreliable.

> https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=198344

> https://github.com/aborche/qemu-guest-agent/issues/19

### virtio_net
Network interfaces for faster networking.

#### [X] Dropping connection to ISP while backing up a PVE-Guest
I've been experiencing something quite strange. While backing up one of my LXCs to an external PBS-Server, my OPNsense installation lost the connection to my ISP. The backup was running on a separate PVE-host. OPNsense wasn't backed up at the same time. **After I've switched over to a emulated E1000 NIC, the problem was gone. Using pci-pass-though can also be an alternative.**

## qemu-guest-agent
Is a small application which allows for communication between QEMU and the guest system. This can enable sme features like file system freeze on backups or reliable shutdowns.

**OPNsense uses a port to FreeBSD developed by [aborche](https://github.com/aborche/qemu-guest-agent). This port does not support all qm-ga features!**

> PVE-Wiki: https://pve.proxmox.com/wiki/Qemu-guest-agent

> OPNsense uses this port to FreeBSD: https://github.com/aborche/qemu-guest-agent

Sometimes... it's the best just to disable or uninstall it.
