# Network architecture

## Purpose

Provide ordinary home connectivity while reducing lateral movement, constraining lower-trust device egress, protecting management interfaces, and adding layered detection without making one security product responsible for every control.

## Logical components

1. **ISP handoff / WAN:** external connectivity; physical details omitted.
2. **OPNsense firewall:** inter-VLAN routing, stateful policy enforcement, NAT and perimeter filtering.
3. **Suricata IDS on OPNsense WAN:** PCAP live-mode inspection using a curated Emerging Threats Open ruleset; alerting only while the rulebase is baselined.
4. **Managed VLAN switch:** VLAN tagging and separation at the access layer.
5. **Managed wireless AP:** maps wireless client groups to purpose-specific VLANs.
6. **Pi-hole + local recursive DNS:** approved resolver and DNS filtering service.
7. **Zenarmor:** application-aware inspection on selected internal VLANs. Restricted-zone policies are scoped by source subnet; unmatched inspected traffic receives the default baseline policy.
8. **Virtualization host:** Proxmox lab environment; exposure is restricted by the relevant firewall policy.
9. **Clients:** administrative, trusted, IoT, cameras, guest, lab and child-device classes.
10. **Hardened travel router:** GL.iNet Beryl AX with an owner-tested WireGuard IPv4 tunnel to OPNsense.

## Trust boundaries

- **WAN → internal:** deny unsolicited inbound traffic except separately reviewed services such as the WireGuard listener.
- **Lower-trust → management:** deny.
- **Client zone → another client zone:** deny by default, with narrowly documented exceptions.
- **Guest → private resources:** deny; allow approved Internet access and captive-portal dependencies.
- **IoT/cameras → internal resources:** deny by default; explicitly permit only required services.
- **DNS:** direct controlled clients to the approved resolver; block traditional external DNS/DoT where required and add application-aware encrypted-DNS controls on restricted Zenarmor policies.
- **Remote access:** authenticate through WireGuard and apply a dedicated firewall policy rather than exposing management services directly to WAN.

## Inspection and enforcement layers

The design deliberately separates responsibilities:

- **OPNsense** is the authoritative L3/L4 trust-boundary enforcement point.
- **Pi-hole** enforces domain-based filtering at the approved resolver.
- **Zenarmor** adds L7 application/category policy to selected internal networks.
- **Suricata** currently provides WAN-side signature detection and telemetry in IDS mode.

Zenarmor and Suricata are not treated as interchangeable. Zenarmor is positioned on selected internal VLANs for application-aware policy; Suricata is positioned on WAN for signature-based IDS. Suricata is not yet permitted to drop packets.

## Packet-flow overview

Internet-bound traffic from an inspected internal VLAN is first governed by the source-zone firewall policy and Zenarmor application policy, then routed/NATed toward WAN. Suricata observes WAN traffic in IDS mode and may generate alerts but does not currently alter forwarding decisions.

Traffic between internal zones is routed through OPNsense and evaluated against zone policy. Switch and AP tagging must align with gateway interface definitions. Exceptions should specify source, destination, protocol/ports, purpose, and regression evidence.

## Assumptions and limitations

This is a logical description, not a wiring diagram. No internal addresses, SSIDs, MACs, device identifiers or production configuration exports are published.

- Segmentation reduces risk but does not eliminate endpoint compromise or misconfiguration.
- L7 classification is not TLS payload decryption.
- DNS filtering cannot guarantee suppression of every telemetry path, hardcoded destination or covert channel.
- WAN IDS may lose internal host attribution after NAT and cannot inspect encrypted payload contents without decryption.
- An IDS signature hit must be validated before being promoted to a blocking action.

See [policy](firewall-policy.md), [current deployment](deployment-and-threat-boundaries.md), and [verification](validation-tests.md).
