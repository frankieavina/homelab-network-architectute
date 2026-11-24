# VLAN Design

**Status: Proposed — not deployed**

## Design approach

The initial rollout will use a staged approach. Infrastructure management remains on the existing default LAN (Option B). The first segmentation targets are guest and IoT networks, subject to hardware support and a reviewed firewall policy.

## Proposed VLANs

| VLAN ID | Name | Subnet | Gateway address (proposed) | Purpose | Initial status |
|---:|---|---|---|---|---|
| 1 | Default / Trusted | Existing LAN | Existing gateway | Existing clients and infrastructure | Current |
| 10 | Management | 192.168.10.0/24 | 192.168.10.1 | Dedicated network-device management | Deferred |
| 20 | Guest | 192.168.20.0/24 | 192.168.20.1 | Guest client network | Proposed |
| 30 | IoT | 192.168.30.0/24 | 192.168.30.1 | IoT client network | Proposed |

VLAN 1 remains unchanged during the initial design and rollout. VLANs 10, 20, and 30 are planning entries only; they do not currently exist in the gateway or switch configuration.

## Initial management strategy (Option B)

Keep the following infrastructure management interfaces on the existing default LAN during initial segmentation:

- UniFi Cloud Gateway Ultra
- NETGEAR GC110P
- NETGEAR GS305E
- Proxmox host
- Raspberry Pi
- Unraid management interface
- Deco management interfaces

A dedicated management VLAN may be introduced later as a separate, controlled migration.

## Switching design intent

### GC110P to gateway

The GC110P physical port 5 is the gateway uplink. The future trunk design must be coordinated with the UniFi gateway configuration. VLAN 1 remains the existing native/untagged network; additional VLANs, if supported and approved, would be tagged.

### GC110P to GS305E

The GC110P physical port 6 and GS305E port 1 form the bedroom-switch uplink. The existing VLAN 1 untagged connectivity must be preserved. Any future tagged VLANs must be configured consistently on both ends.

The PS5 and desktop PC are intended to remain on the default trusted network unless a later design decision changes their placement.

### Server and service ports

The Proxmox port may eventually require a trunk if virtual machines or containers need access to multiple VLANs. Do not configure it as a trunk until the host bridge and guest networking plan are documented.

The Raspberry Pi currently provides Pi-hole DNS and other services. If client VLANs are introduced, the firewall must explicitly allow required DNS traffic to the Pi-hole address, or the DNS architecture must be changed first.

The Unraid port should remain on the existing network during the initial rollout unless a specific service or VM requirement justifies a change.

### Wireless access

The current Deco W6000 system has not been verified to map individual SSIDs to 802.1Q VLANs. Therefore, it cannot currently be used as the assumed access path for VLAN 20 or VLAN 30. Do not assign those VLANs to the Deco switch port or describe its SSIDs as VLAN-isolated without new authoritative evidence.


### Current wireless decision

The Deco W6000 system will remain in Access Point mode for now. Both Deco units and the existing SSIDs (Rocky's Net, Guest Network, and IoT Network) will be retained. Replacing or adding a VLAN-capable access point is deferred until a later investigation. Until per-SSID VLAN tagging is verified, the existing Guest and IoT SSIDs must not be represented as separate VLANs or as VLAN-isolated networks. The main Deco remains connected to GC110P physical port 4 on the existing VLAN 1 network.

## Firewall policy objectives

The following are design objectives, not configured rules:

| Source | Destination | Intended policy |
|---|---|---|
| Guest (VLAN 20) | Internet | Allow |
| Guest (VLAN 20) | Trusted LAN (VLAN 1) | Deny |
| Guest (VLAN 20) | Management interfaces | Deny |
| IoT (VLAN 30) | Internet | Allow as required |
| IoT (VLAN 30) | Trusted LAN (VLAN 1) | Deny by default; add narrow exceptions |
| IoT (VLAN 30) | Required local services | Allow only documented requirements |
| Trusted LAN (VLAN 1) | Other VLANs | Administrative access as needed |
| Management (VLAN 10) | Network infrastructure | Deferred until VLAN 10 is implemented |

If Pi-hole remains on VLAN 1, clients on VLAN 20 and VLAN 30 will need explicit DNS access to it (TCP/UDP port 53). Additional service discovery or device-control exceptions must be identified and tested rather than broadly allowing inter-VLAN traffic.

## Preconditions before implementation

1. Confirm the gateway's VLAN and firewall behavior for the intended topology.
2. Confirm the GS305E tagging and PVID behavior from its manual and current configuration.
3. Decide how guest and IoT clients will physically or wirelessly enter their VLANs. The existing Deco cannot be assumed to provide per-SSID VLAN tagging.
4. Define DHCP scopes and DNS behavior for each new network.
5. Define firewall rules and required exceptions.
6. Prepare a rollback plan and preserve existing VLAN 1 connectivity.
7. Implement one change at a time and validate before proceeding.

## Validation checklist

- [ ] New VLAN appears on the gateway and switch as intended.
- [ ] Tagged uplinks carry only the required VLANs.
- [ ] Access ports assign untagged traffic to the intended PVID.
- [ ] Clients receive the correct DHCP address, gateway, and DNS settings.
- [ ] DNS resolution works as designed.
- [ ] Guest clients cannot reach trusted LAN or management interfaces.
- [ ] IoT clients cannot initiate unauthorized connections to trusted devices.
- [ ] Required services continue to work.
- [ ] Existing VLAN 1 clients and infrastructure remain reachable.
- [ ] Rollback procedure is available and tested where practical.
