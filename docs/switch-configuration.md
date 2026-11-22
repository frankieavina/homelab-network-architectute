# Switch Configuration Record

This document records the observed switch configuration before VLAN segmentation. It is a baseline for future changes, not a configuration script.

> **Current state:** The homelab is operating on one active LAN / VLAN (VLAN 1). VLANs 10, 20, and 30 are proposed for future use only. No switch changes were made when this document was published.

## Core switch — NETGEAR GC110P

### Global VLAN settings

- 802.1Q VLAN functionality is enabled.
- VLAN 1 (Default) exists and is the current LAN VLAN.
- VLAN 4089 (Auto-Video) is a factory/default entry. Auto-Video is disabled.
- No additional user-created VLANs are currently configured.

### Port modes and native VLAN

The switch interface labels ports as g1 through g10. The physical copper ports relevant to this network are 1–8.

| Physical port | Connected device | Current mode | Native VLAN |
|---:|---|---|---:|
| 1 | Proxmox host | General | 1 |
| 2 | Raspberry Pi 5 | General | 1 |
| 3 | Unraid server | General | 1 |
| 4 | Main Deco W6000 AP | General | 1 |
| 5 | UniFi Cloud Gateway Ultra uplink | Trunk | 1 |
| 6 | GS305E bedroom switch | General | 1 |
| 7–8 | Unused | General | 1 |

### Current VLAN membership and PVID observations

- Ports 1–4 and 6–8 currently use PVID 1.
- VLAN 1 is the untagged member on the active endpoint-facing ports.
- Port 5 is the router uplink. It is configured in Trunk mode with native VLAN 1.
- The switch UI shows a broad allowed-VLAN range on the trunk. This is an allowed-list setting; it does **not** mean those VLANs have been created or are active members.
- The observed membership table lists VLAN 1 and the default Auto-Video VLAN 4089 on port 5. Auto-Video is disabled.
- Ingress filtering is enabled on port 5 and disabled on the other listed ports, according to the observed settings.
- Native VLAN tagging is disabled.

The current configuration carries the existing LAN on VLAN 1. The broad trunk allowed list should not be confused with deployed VLAN membership.

## Bedroom switch — NETGEAR GS305E

### Current VLAN settings

- 802.1Q VLAN configuration is in use.
- VLAN 1 is the only VLAN shown in the current membership table.
- Ports 1–5 are members of VLAN 1.
- Ports 1–5 have PVID 1.

### Physical port map

| Port | Connected device | Current VLAN / PVID |
|---:|---|---|
| 1 | Uplink to GC110P port 6 | VLAN 1 / PVID 1 |
| 2 | PlayStation 5 | VLAN 1 / PVID 1 |
| 3 | Desktop PC | VLAN 1 / PVID 1 |
| 4–5 | Unused | VLAN 1 / PVID 1 |

The GS305E uplink and the corresponding GC110P port currently carry the existing LAN on VLAN 1.

## Current network behavior

- The router provides the default gateway and DHCP service for the single LAN.
- Wired endpoints, servers, switches, and the Deco access point are currently connected to that LAN.
- The Deco W6000 is in Access Point mode. Its separate SSIDs have not been verified as separate VLANs.
- The current configuration should be treated as a flat LAN for segmentation planning. SSID names and switch trunk mode alone do not establish network isolation.

## Future segmentation considerations

The following are design considerations only; do not apply them until the router networks and switch configuration have been planned together.

1. **Router uplink (GC110P port 5):** Coordinate the router and switch so VLAN 1 remains the native/untagged network and any future VLANs are tagged consistently.
2. **Bedroom switch uplink (GC110P port 6 ↔ GS305E port 1):** Both ends must agree on VLAN membership and tagging before carrying additional VLANs.
3. **Client ports:** Keep the PC and PlayStation 5 on the intended access VLAN unless there is a deliberate reason to change them.
4. **Server ports:** Decide which VLANs each server or hypervisor actually needs. Do not turn every server connection into a trunk by default.
5. **Wireless:** Do not assign Guest or IoT wireless clients to separate VLANs until the access point is confirmed to support SSID-to-VLAN mapping.
6. **Management access:** Preserve access to the router and switch management interfaces during every change. The initial design keeps management on VLAN 1 until segmentation is stable.

## Change control checklist

Before any future switch change:

- [ ] Save or record the current router and switch settings.
- [ ] Confirm the VLAN exists on the router and define its subnet, DHCP, and DNS settings.
- [ ] Confirm VLAN membership and tagging on both ends of each link.
- [ ] Confirm the PVID and untagged VLAN for every access port.
- [ ] Confirm management access and a recovery path.
- [ ] Test DHCP, DNS, gateway access, and intended isolation from a client on the new VLAN.
- [ ] Update this document with the actual change and validation results.

## Reference

Configuration details are based on the NETGEAR GC110P and GS305E settings reviewed during the homelab network design process, along with the GC110P user manual. Re-check the live switch interface before making changes, as settings may have changed since this baseline was recorded.
