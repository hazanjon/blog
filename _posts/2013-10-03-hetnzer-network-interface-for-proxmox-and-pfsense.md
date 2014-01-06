---
layout: post
title:  "Hetnzer Network Interface settings for Proxmox and pfSense"
date:   2013-10-03 20:00:00
categories: 
---

To setup Proxmox with a pfSense firewall you will need to at least 1 Additional IP. This is used to run both the pfSense VM and any VM's hidden behind.

```
### Hetzner Online AG - installimage
# Loopback device:
auto lo
iface lo inet loopback

# device: eth0
auto eth0 ##Hetzner Interface
iface eth0 inet static
    address     <Main IP>
    broadcast   <Broadcast IP>
    netmask     255.255.255.224
    gateway     <Gateway IP>
    pointopoint <Gateway IP>

    # default route to access subnet
    up route add -net <Hetzner Route> netmask 255.255.255.224 gw <Gateway IP> eth0

auto vmbr0 ##Main Interface - Used for pfSense and any DMZ VM's
iface vmbr0 inet static
    address     <Main Hetzner IP>
    netmask     255.255.255.224
    bridge_ports none
    bridge_stp  off
    bridge_fd   0

    ##Any Extra IP's (You need at least 1 for the pfSense VM and 1 for every DMZ VM)
    up ip route add <Additional IP>/32 dev vmbr0

auto vmbr1 ##pfSense LAN - DHCP on this is controlled by pfSense
iface vmbr1 inet static
    address     10.0.0.254
    netmask     255.255.255.0
    gateway     <pfSense IP 10.0.0.x>
    bridge_ports none
    bridge_stp  off
    bridge_fd   0

auto vmbr2 ##Internal network between VM's
iface vmbr2 inet manual
    bridge_ports none
    bridge_stp  off
    bridge_fd   0

iface eth0 inet6 static
    address     <Main IPv6>
    netmask     64
    gateway     fe80::1
```
