# Changelog

Significant documentation and network architecture changes are recorded here.

## 2026-11-22

- Created the network architecture documentation repository.
- Documented the verified physical topology and switch port assignments.
- Recorded the current single-LAN baseline (VLAN 1).
- Added the staged VLAN proposal: VLAN 10 Management (deferred), VLAN 20 Guest, and VLAN 30 IoT.
- Selected Option B: keep infrastructure management on VLAN 1 during initial segmentation.
- Recorded the Deco W6000 limitation: per-SSID VLAN tagging is not verified.
- No network configuration changes were made as part of this documentation milestone.

## 2026-11-22

- Added a proposed logical VLAN architecture diagram, clearly separated from the verified current physical topology.
- Linked the logical VLAN diagram from the repository README.
- Documented deployment constraints, including the unverified Deco per-SSID VLAN capability and the need to define endpoint access paths before VLAN rollout.

## 2026-11-23

- Recorded the decision to retain the existing Deco W6000 access points in AP mode and defer wireless VLAN alternatives or upgrades.
- Clarified that existing Guest and IoT SSIDs are not confirmed as VLAN-isolated networks.
- No wireless or network configuration changes were made.
