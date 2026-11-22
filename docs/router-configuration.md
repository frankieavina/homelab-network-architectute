# Router and Gateway Configuration Record

This document records the known role and baseline configuration of the UniFi Cloud Gateway Ultra in the homelab. It separates confirmed current behavior from settings that still need to be inventoried.

> **Current state:** The gateway is operating the homelab's single LAN. This document is descriptive only; no router, DHCP, DNS, or firewall settings were changed.

## Gateway device

| Item | Current configuration |
|---|---|
| Device | Ubiquiti UniFi Cloud Gateway Ultra |
| Network role | Router, default gateway, firewall, and DHCP server |
| LAN design | One active Default network / VLAN 1 |
| IPv4 addressing | Private /24 LAN |
| DHCP server | UniFi gateway |
| DNS for LAN clients | Raspberry Pi 5 running Pi-hole |
| VLAN segmentation | Not yet deployed |

Exact private IP addresses and reservation values are intentionally omitted from this public document.

## LAN and DHCP

- The current LAN is the UniFi Default network, associated with VLAN 1.
- The gateway provides DHCP addresses to clients on the LAN.
- DHCP reservations are used for key infrastructure, including the managed switches, Unraid server, Raspberry Pi, and Deco access point / mesh node.
- The DHCP scope is configured within the existing private /24 network.
- The current DHCP service remains on the gateway; DHCP was not moved to Pi-hole.

The live DHCP scope, lease duration, and complete reservation list should be reviewed in the UniFi interface before any future address-plan changes. Exact reservation values are kept out of this public portfolio.

## DNS

- The Raspberry Pi 5 runs Pi-hole as the LAN DNS filtering service.
- The UniFi LAN DHCP / DNS settings direct clients to Pi-hole.
- Pi-hole uses an external upstream DNS resolver.
- The gateway remains the DHCP server; Pi-hole is not acting as the DHCP server.

Any future VLAN network must have a deliberate DNS configuration. If Pi-hole remains on the current trusted LAN, firewall policy must permit clients on approved networks to reach it for DNS without granting unnecessary access to other trusted devices.

## VLAN and network status

Only the Default network is currently configured for the homelab LAN. The following networks are planning proposals and are not active:

| VLAN ID | Proposed network | Status |
|---:|---|---|
| 1 | Default / Trusted | Active |
| 10 | Management | Proposed only |
| 20 | Guest | Proposed only |
| 30 | IoT | Proposed only |

The initial migration strategy is to keep infrastructure management on the existing Default network while wired and wireless segmentation is designed and validated.

## Firewall configuration

The gateway provides the network firewall, but a complete rule-by-rule inventory has not yet been captured for this documentation project.

Do not infer that the future Guest or IoT isolation policies are already active. They are design goals for a later implementation:

- Guest clients should have internet access while being isolated from trusted LAN and management devices.
- IoT clients should be restricted to the services and destinations they require.
- Authorized trusted devices should retain access to infrastructure management interfaces.
- DNS access to Pi-hole should be explicitly allowed for any segmented network that uses it.

Before implementing segmentation, record the existing firewall rules and confirm the gateway's rule ordering and behavior in the current UniFi Network application.

## Future validation checklist

- [ ] Record the current Default network settings and gateway address in a private administrative inventory.
- [ ] Review the DHCP range, lease settings, and reservation list.
- [ ] Confirm the DNS server distributed to clients by DHCP.
- [ ] Export or record the current firewall policy before making changes.
- [ ] Create any new VLAN networks on the gateway before configuring switch ports for them.
- [ ] Define DHCP, gateway, and DNS settings for each new network.
- [ ] Add and test firewall rules for Guest, IoT, Management, and required DNS access.
- [ ] Verify client addressing, DNS resolution, internet access, and intended isolation on each VLAN.

## Documentation boundaries

This record includes only settings established during the homelab planning process. It does not claim to document every UniFi setting, firewall rule, WAN parameter, firmware version, or security feature. Those details should be added only after they are checked against the live gateway.
