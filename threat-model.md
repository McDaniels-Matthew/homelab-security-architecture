# Threat model

## Scope

Consumer and lab network infrastructure, including the security gateway, managed switching/Wi-Fi, DNS filter, virtualization host and attached client classes. The purpose is educational engineering analysis, not a formal penetration test.

## Assets to protect

- Firewall, switch and AP administrative interfaces.
- Trusted endpoints and personal data.
- Cameras and recordings.
- DNS service availability and integrity.
- Lab systems and virtual machines.

## Adversary assumptions

| Actor / entry point | Plausible objective |
|---|---|
| Compromised IoT device | Scan or laterally access trusted hosts |
| Untrusted guest client | Reach internal resources or management |
| Malware on trusted endpoint | Reach infrastructure management |
| Internet-based attacker | Exploit exposed perimeter service |
| Configuration mistake | Accidentally permit cross-zone access |

## Threats and controls

| Threat | Primary control | Residual risk / validation need |
|---|---|---|
| Lateral movement from IoT | Inter-zone default-deny policy | Confirm with reachability tests; monitor exceptions |
| Guest access to LAN | Guest isolation and WAN-only rules | Test both IPv4 and IPv6 if enabled |
| Management plane exposure | Dedicated management zone and access rules | Validate admin service reachability from all zones |
| DNS bypass | Approved DNS resolver policy | Encrypted DNS and hardcoded resolvers may require distinct treatment |
| Rogue or mis-tagged client | Port/SSID VLAN mapping | Verify switch access-port and AP profiles |
| Disruption to DNS/firewall | Config backup and recovery procedures | Recovery testing not yet documented |

## Out of scope

Physical access, ISP infrastructure security, endpoint malware detection effectiveness, and comprehensive wireless protocol assessments.

## Review triggers

Revisit this threat model after VLAN changes, public-service exposure, new remote-access connections, major firmware changes, or deployment of additional monitoring systems.
