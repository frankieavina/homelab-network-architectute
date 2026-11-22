# Network Overview

## Scope

This document describes the homelab network at a high level. It separates verified current-state information from future design decisions.

## Current state

- The network currently uses one LAN: the default network, VLAN 1.
- The UniFi Cloud Gateway Ultra is the router, firewall, and DHCP server.
- The NETGEAR GC110P is the main managed switch.
- A NETGEAR GS305E provides additional wired connectivity in the bedroom.
- TP-Link Deco W6000 units provide wireless coverage in Access Point mode.
- The Deco system is treated as VLAN-unaware for this design because per-SSID 802.1Q VLAN assignment has not been verified in its documentation or available app settings.
- Separate Deco SSIDs are not assumed to represent separate VLANs or equivalent network isolation.

## Design decision: staged segmentation

The initial approach is to keep infrastructure management on VLAN 1 while planning separate client networks. This avoids introducing management reachability changes at the same time as the first VLAN deployment.

Future segmentation candidates:

| VLAN | Name | Proposed subnet | Status |
|---:|---|---|---|
| 10 | Management | 192.168.10.0/24 | Deferred |
| 20 | Guest | 192.168.20.0/24 | Proposed |
| 30 | IoT | 192.168.30.0/24 | Proposed |

No additional VLANs are currently deployed.

## Design requirements

1. Preserve the existing LAN and its working DHCP reservations during the initial rollout.
2. Maintain administrative access to the gateway and managed switches.
3. Allow guest clients internet access while denying access to trusted LAN resources.
4. Restrict IoT devices from initiating unnecessary connections to trusted devices.
5. Permit explicitly required services, such as DNS to the Pi-hole resolver, where the final design requires them.
6. Configure switch trunks and access ports consistently at both ends.
7. Validate each change before proceeding to the next.
8. Maintain a rollback path for every network change.

## Important limitations

The existing Deco W6000 configuration has not demonstrated per-SSID VLAN tagging. Therefore, the current wireless guest and IoT SSIDs must not be represented as VLAN 20 and VLAN 30. Wireless VLAN segmentation may require a VLAN-capable access point in a later phase.

## Change control

All diagrams and plans in this repository must label each item as one of:

- **Current:** verified in the running environment.
- **Proposed:** design intent, not yet configured.
- **Implemented:** configured and validated in the running environment.

See [VLAN Design](vlan-design.md) and [Physical Topology](physical-topology.md).
