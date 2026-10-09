# Network architecture

## Purpose

Provide ordinary home connectivity while reducing lateral movement and exposure of management interfaces. The network is designed as a set of security zones enforced by a dedicated firewall.

## Logical components

1. **ISP handoff / WAN:** external connectivity; physical details omitted.
2. **OPNsense firewall:** inter-VLAN policy enforcement, routing and perimeter filtering.
3. **Managed VLAN switch:** VLAN tagging and separation at the access layer.
4. **Managed wireless AP:** maps wireless client groups to the appropriate VLANs.
5. **Pi-hole DNS service:** shared DNS filtering where explicitly allowed by policy.
6. **Virtualization host:** Proxmox lab environment; exposure is restricted by the relevant access policies.
7. **Clients:** administrative, trusted, IoT, cameras, guest and child-device classes.

## Trust boundaries

- WAN → internal network: deny unsolicited inbound traffic unless a separately reviewed exception is required.
- Lower-trust → management: deny.
- Client zone → another client zone: deny by default, with narrowly documented exceptions.
- Guest → private resources: deny; allow approved internet access and required supporting services.
- DNS flows: direct approved resolvers; restrict bypass according to policy.

## Packet-flow overview

Traffic between zones is routed through OPNsense and evaluated against zone-based policy. Switch and AP VLAN tagging must align with gateway interface definitions. Exceptions should specify source, destination, ports, purpose and test evidence.

## Assumptions and limitations

This is a logical diagram, not a wiring diagram. No internal addressing, device identifiers or configuration exports are published. Segmentation reduces risk but does not eliminate device compromise, side-channel access, misconfiguration, or weaknesses in shared infrastructure. Internet access policies alone do not demonstrate resistance to DNS-over-HTTPS or tunneling.

See [diagram](../diagrams/network-overview.svg), [policy](firewall-policy.md), and [verification](validation-tests.md).
