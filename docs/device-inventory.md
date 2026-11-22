# Device Inventory

This document records the physical and virtual infrastructure used in the homelab network. It distinguishes equipment currently in service from equipment that is still being prepared.

> **Public-repository note:** Exact private IP addresses, MAC addresses, serial numbers, credentials, tokens, and other sensitive identifiers are intentionally omitted. Device models and general roles are included for portfolio documentation.

## Network infrastructure

| Device | Model | Role | Status |
|---|---|---|---|
| Router / firewall | Ubiquiti UniFi Cloud Gateway Ultra | Internet gateway, routing, firewall, and DHCP for the current LAN | Operational |
| Core managed switch | NETGEAR GC110P | Main wired distribution switch connecting the router, servers, Raspberry Pi, access point, and bedroom switch | Operational |
| Secondary managed switch | NETGEAR GS305E | Extends wired connectivity to the bedroom PC and PlayStation 5 | Operational |
| Wireless access point / mesh system | TP-Link Deco W6000 | Provides the primary wireless network and wireless mesh coverage | Operational; Access Point mode |
| Mesh node | TP-Link Deco W6000 | Extends wireless coverage in the living room | Operational; wireless backhaul |

## Compute and services

| Device | Hardware | Role | Status |
|---|---|---|---|
| Raspberry Pi | Raspberry Pi 5 | Network services and monitoring host, including Pi-hole DNS, Uptime Kuma, CUPS, system health monitoring, and scheduled backups | Operational |
| NAS / virtualization server | Supermicro X11SCA motherboard, Intel Core i5-8500T, 32 GB DDR4 RAM | Unraid storage, Docker applications, and virtual machines | Operational; not necessarily powered on continuously |
| Small-form-factor virtualization node | Lenovo ThinkCentre M720q, Intel Core i5-9500T, 16 GB RAM, 256 GB NVMe | Proxmox virtualization host | Proxmox installed; storage upgrade and further setup pending |

### Unraid server components

- **Storage:** Two 6 TB SAS drives (one parity and one data), a 256 GB Samsung NVMe drive, and a 4 TB WD hard drive assigned to the `hdd-cache` BTRFS pool.
- **SAS controller:** Adaptec ASR-7805 HBA.
- **Cooling:** Noctua CPU cooler and additional case / HBA airflow.
- **Power supply:** EVGA 600 W, 80 Plus.
- **Chassis:** Cooler Master N400.

Applications and virtual machines currently hosted on Unraid include Cloudflare, code-server, Immich, Immich PostgreSQL, macinabox, Nginx Proxy Manager, Windows Server 2022, multiple Windows 11 VMs, Fedora, and macOS Ventura.

## Endpoint devices

| Device | Connection | Role | Status |
|---|---|---|---|
| Desktop PC | Wired through the GS305E | Primary desktop endpoint | Operational |
| PlayStation 5 | Wired through the GS305E | Gaming / entertainment endpoint | Operational |

## Physical switch connections

### NETGEAR GC110P

| Physical port | Connected device |
|---|---|
| 1 | Proxmox host — Lenovo ThinkCentre M720q |
| 2 | Raspberry Pi 5 |
| 3 | Unraid server |
| 4 | Main TP-Link Deco W6000 access point |
| 5 | UniFi Cloud Gateway Ultra uplink |
| 6 | NETGEAR GS305E bedroom switch |
| 7–8 | Unused |

The switch interface may label ports using the `g1`–`g10` convention. In the physical copper port map, the router uplink is copper port 5 and the bedroom-switch uplink is copper port 6.

### NETGEAR GS305E

| Physical port | Connected device |
|---|---|
| 1 | Uplink to NETGEAR GC110P port 6 |
| 2 | PlayStation 5 |
| 3 | Desktop PC |
| 4–5 | Unused |

## Network design notes

- The current network operates as a single LAN / VLAN. Additional VLANs are design proposals only and have not been deployed.
- The GC110P and GS305E support 802.1Q VLAN functionality.
- The Deco W6000 system is operating in Access Point mode. Its available documentation and app settings have not established support for assigning separate VLAN tags to individual SSIDs. The current wireless SSIDs should therefore not be represented as separate VLANs.
- The Raspberry Pi provides DNS through Pi-hole for the current network. Any future segmentation must preserve the required DNS reachability.
- Device roles, port connections, and operational status should be updated when equipment or cabling changes.
