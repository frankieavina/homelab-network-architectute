# IP Addressing Plan

This document describes the homelab's current addressing approach and the proposed direction for future network segmentation.

> **Public-repository note:** Exact device IP addresses, MAC addresses, DHCP reservation values, and other identifying network details are intentionally not published here. This document describes the strategy without exposing the live address inventory.

## Current state

The homelab currently uses a single private IPv4 LAN:

- One default network / VLAN (VLAN 1).
- A /24 subnet.
- The UniFi Cloud Gateway Ultra acts as the default gateway, router, firewall, and DHCP server.
- DHCP reservations are used for infrastructure devices and servers that benefit from predictable addresses.
- No additional user-created VLAN networks have been deployed.

The existing network includes reservations for the gateway, managed switches, Unraid server, Raspberry Pi, and Deco access point / mesh node. The Proxmox host's final address assignment is not recorded in this public document.

## Address assignment approach

| Device category | Recommended method | Reason |
|---|---|---|
| Router / firewall | Stable gateway address | Default route for the LAN |
| Managed switches | DHCP reservation or documented static address | Predictable management access |
| Servers and network services | DHCP reservation or documented static address | Stable service endpoints and monitoring |
| Wireless access points / mesh nodes | DHCP reservation or documented static address | Predictable infrastructure management |
| General client devices | DHCP | Centralized, flexible address assignment |
| Guest and IoT clients (future) | DHCP on their respective VLAN networks | Separate address scopes and policy enforcement |

For this homelab, DHCP reservations are managed centrally on the UniFi gateway. Any future static addresses should be documented and excluded from dynamic DHCP allocation to avoid conflicts.

## Proposed VLAN and subnet design

The following is a **proposed design**, not a description of the live configuration. These VLANs and subnets have not yet been created or assigned to switch ports.

| VLAN ID | Network name | Proposed subnet | Intended use |
|---:|---|---|---|
| 1 | Default / Trusted | Existing LAN subnet | Current trusted devices and services during the initial migration |
| 10 | Management | 192.168.10.0/24 | Network infrastructure management interfaces, when migration is appropriate |
| 20 | Guest | 192.168.20.0/24 | Guest client devices, with access restricted from trusted networks |
| 30 | IoT | 192.168.30.0/24 | IoT devices, with access limited to required services |

The proposed VLAN 10 management network is intentionally deferred. The initial approach is to keep infrastructure management on the existing default network until segmentation is stable.

## DHCP and DNS considerations

- The UniFi Cloud Gateway Ultra currently provides DHCP for the LAN.
- Pi-hole runs on the Raspberry Pi and provides DNS filtering for the current network.
- If Guest or IoT networks are introduced, their clients will need an explicitly permitted path to the DNS service.
- Firewall policy should allow only the DNS traffic required to reach Pi-hole (typically UDP and TCP port 53), rather than broadly permitting access to the trusted LAN.
- DHCP scope, gateway, DNS server, and lease settings should be defined separately for each new VLAN network.

## Future firewall policy objectives

| Source network | Destination | Intended policy |
|---|---|---|
| Guest | Internet | Allow required internet access |
| Guest | Trusted LAN / Management | Deny by default |
| IoT | Internet | Allow only as required |
| IoT | Trusted LAN / Management | Deny by default, with narrowly scoped exceptions |
| Trusted LAN | Infrastructure management | Allow for authorized administration |
| Management | Network infrastructure | Allow authorized management traffic |
| Guest / IoT | Pi-hole DNS | Permit only required DNS traffic if Pi-hole remains on the trusted network |

These are policy objectives for a future implementation, not active firewall rules.

## Implementation dependencies and cautions

1. Create and configure each VLAN network on the router before assigning access or trunk ports to it.
2. Coordinate VLAN membership, tagging, and PVID settings across both ends of every switch link.
3. Preserve access to the router and switch management interfaces throughout the migration.
4. Confirm that wireless access points support the required SSID-to-VLAN mapping before planning wireless clients on separate VLANs. The current Deco W6000 setup has not been shown to provide this capability.
5. Validate DHCP, DNS, gateway reachability, and firewall behavior on each VLAN before moving additional devices.
6. Keep a record of changes and a tested recovery path before modifying production switch ports.

## Change status

- [x] Document current single-LAN architecture.
- [x] Document the proposed VLAN and subnet strategy.
- [ ] Create VLAN 10, 20, and 30 on the router.
- [ ] Configure switch VLAN membership, tagging, and PVIDs.
- [ ] Validate inter-VLAN firewall policy and DNS reachability.
- [ ] Evaluate VLAN-capable wireless access point options if wireless segmentation is required.

No network configuration changes are made by publishing this document.
