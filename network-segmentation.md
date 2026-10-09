# Segmentation design

Security zones are identified by purpose rather than publishing real VLAN IDs, addressing or SSIDs.

| Zone | Typical members | Intent |
|---|---|---|
| Management | Firewall, switch, AP administration | Admin-only access; not a general browsing network |
| Trusted | Personal computers and managed endpoints | Standard user access with controlled infrastructure exceptions |
| IoT | Smart-home equipment | Internet/services as required; isolate from trusted and management |
| Cameras | Surveillance devices | Minimize outbound and cross-zone reachability |
| Guest | Visitor devices | Internet only and captive portal |
| Child devices | Age-appropriate endpoints | Restricted destinations; allowlisting is a work in progress |

## Principles

1. Deny inter-zone traffic unless explicitly required.
2. Place administration behind narrower source controls than ordinary client traffic.
3. Define service-level exceptions rather than broad `any` rules.
4. Account for required DNS, DHCP, NTP and device discovery separately.
5. Validate IPv4 and IPv6 paths, as applicable.
6. Record every exception's business or household justification.

## Important distinction

A VLAN is a logical separation mechanism, not by itself a security guarantee. The effective security boundary depends on correct tagging, routed firewall policy, management-plane controls and verification.
