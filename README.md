# Homelab Network Architecture

A practical documentation project covering the design, implementation, and validation of a small-enterprise-style homelab network.

The project documents the existing network before introducing controlled improvements such as VLAN segmentation, inter-VLAN firewall policies, and infrastructure management practices.

## Project goals

- Document the physical network topology and device inventory.
- Establish a clear IP addressing and network segmentation plan.
- Design VLANs and firewall policies before implementation.
- Document managed-switch configuration and port assignments.
- Validate connectivity, isolation, and expected network behavior.
- Maintain diagrams and change records as the environment evolves.

## Current environment

The network currently operates as a single LAN using the default network (VLAN 1). A UniFi Cloud Gateway Ultra provides routing, firewalling, and DHCP. A NETGEAR GC110P serves as the main managed switch, with a NETGEAR GS305E downstream switch. Wireless access is provided by TP-Link Deco W6000 units operating in Access Point mode.

Infrastructure management will remain on the existing default LAN during the initial segmentation phase (Option B). No VLAN changes are considered implemented until configuration and validation are documented.

## Proposed segmentation

| VLAN | Name | Proposed subnet | Purpose |
|---:|---|---|---|
| 1 | Default / Trusted | Existing LAN | Existing trusted clients and infrastructure during initial rollout |
| 10 | Management | 192.168.10.0/24 | Future dedicated infrastructure management network |
| 20 | Guest | 192.168.20.0/24 | Guest client isolation |
| 30 | IoT | 192.168.30.0/24 | Smart-home and IoT device isolation |

**Status:** Proposed only. VLANs 10, 20, and 30 have not been created or deployed.

The initial implementation will prioritize VLANs 20 and 30 only after confirming that the gateway, switches, and wireless access points can support the intended design. Infrastructure management will remain on VLAN 1 initially.

## Repository structure

- `docs/` — architecture, topology, addressing, VLAN, and policy documentation
- [Proposed logical VLAN architecture](docs/proposed-logical-vlan-architecture.md) — logical segmentation diagram and implementation constraints
- `diagrams/` — network diagrams and visual references
- `CHANGELOG.md` — meaningful architecture and implementation changes

## Related homelab project

The Raspberry Pi 5 provides several supporting network services for this environment, including Pi-hole DNS, Uptime Kuma monitoring, CUPS printing, system health monitoring, and automated Restic backups to Unraid.

Those services are documented separately to keep each project focused and avoid duplicating implementation details:

- [Homelab Raspberry Pi Services](https://github.com/frankieavina/homelab-raspberry-pi-services) — service deployment, monitoring, maintenance, and backup documentation.

Together, these repositories document both the network infrastructure and the services operating on it.

## Documentation principles

- Distinguish clearly between **current state**, **proposed design**, and **implemented configuration**.
- Do not publish passwords, tokens, private keys, recovery codes, or exported configuration files containing secrets.
- Sanitize public-facing examples where exact internal addressing or identifiers are not needed.
- Record the reason, impact, validation, and rollback plan for configuration changes.
- Make no production network changes solely to match a diagram or document.

## Project status

**Phase 4 — Network Architecture & Documentation:** In progress.

The first milestone is documenting the verified current-state topology and baseline configuration. VLAN implementation will follow only after the design and dependencies are reviewed.
