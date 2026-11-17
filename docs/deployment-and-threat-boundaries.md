# Deployment state and trust-boundary assessment

**Snapshot: 2026-11-16.** This document distinguishes deployed controls from controls that are only configured, under observation, or pending validation. All results are owner-reported unless accompanied by sanitized verification evidence.

## 1. Assets and threat model

- **Protect:** management interfaces, trusted household endpoints, household data, camera resources, virtualization services, DNS integrity, and remote-access credentials.
- **Less trusted:** smart TVs and other IoT devices, cameras, guests, child endpoints, and unmanaged clients.
- **Boundaries:** WAN to gateway; restricted VLANs to trusted/management; clients to approved DNS; remote VPN clients to explicitly permitted internal resources.
- **Threats in scope:** compromised IoT firmware, lateral movement, misconfigured exceptions, unsolicited WAN access, DNS-policy bypass, unnecessary telemetry, malicious destinations, exploit/C2 traffic, and unsafe remote access.

## 2. Current deployed controls

| Boundary / function | Current control | What is established | Residual exposure |
| --- | --- | --- | --- |
| WAN → home | Stateful OPNsense policy; no general inbound exposure | Unsolicited inbound access denied unless explicitly allowed | Allowed services still require patching and review |
| Remote access | WireGuard listener with separate tunnel policy | Authenticated tunnel path exists | Full field fail-closed/DNS/IPv6 matrix remains incomplete |
| IoT/cameras/guest → trusted/management | Separate VLANs and explicit inter-zone firewall restrictions | Baseline isolation behavior owner-reported | New exceptions or IPv6 differences can reopen paths |
| Guest access | Internet-only firewall policy plus captive portal | Guest can be separated from private resources while retaining approved Internet use | Portal itself is not a security boundary |
| Approved DNS | Pi-hole with local recursive resolution | Controlled clients use an approved resolver | Resolver availability is operationally significant |
| Traditional DNS bypass | External port 53 and DoT controls on restricted zones | Common direct resolver paths are constrained | HTTPS/QUIC tunneling requires L7 controls and testing |
| Application-aware filtering | Zenarmor on selected internal VLANs | Subnet-scoped custom policies for restricted zones; baseline fallback policy for other inspected traffic | Classification is not equivalent to TLS decryption |
| Smart-TV/IoT privacy | Pi-hole domain controls, host-specific firewall observation, Zenarmor IoT policy | Known telemetry/ad categories and encrypted-DNS applications can be restricted while required services are observed | No claim of eliminating every ACR/telemetry path |
| WAN threat detection | Suricata PCAP live-mode IDS on WAN, curated ET Open rules, Hyperscan, syslog/EVE | Signature alerts can be generated without altering packet forwarding | Rules are not yet fully baselined; IPS/drop mode is not enabled |

## 3. Zenarmor policy model

The current design uses a small number of application-policy classes rather than one policy per device. Restricted-zone policies are matched by source subnet to avoid dependence on VLAN metadata presented by the local inspection interface.

Conceptually:

- **IoT restricted:** malware/phishing/hacking/C2 controls, encrypted-DNS application blocking, VPN/proxy restrictions, ad filtering and DNS anomaly inspection.
- **Camera restricted:** similar baseline with tighter treatment of newly seen/recovered destinations and unnecessary remote-access/VPN behavior.
- **Guest restricted:** strict malicious-site controls, encrypted-DNS/VPN/proxy restrictions, SafeSearch and content/category controls.
- **Child restricted:** strongest content/circumvention controls, SafeSearch, remote-access restrictions and DNS anomaly protection.
- **Default baseline:** security-oriented protection for inspected traffic not matched by a restricted policy; intended to avoid unnecessary acceptable-use restrictions on trusted/admin workflows.

Production policy exports and internal addressing are intentionally not published.

## 4. Suricata state

Suricata is enabled through OPNsense's integrated Intrusion Detection service with the following current role:

- WAN only.
- PCAP live mode (**IDS / alert-only**).
- Hyperscan pattern matcher.
- Syslog alerts and EVE output enabled.
- Curated high-value Emerging Threats Open categories downloaded.
- No blanket IPS/drop policy.

The next engineering step is alert baselining: identify false positives/noise, verify useful detections, and only then consider selective high-confidence drop rules.

## 5. What is not yet established

- Comprehensive proof that all DoH/DoQ or non-DNS telemetry paths are prevented.
- Complete elimination of LG TV ACR/telemetry.
- TLS interception of smart-TV or general client traffic.
- Fully tuned Suricata IPS behavior; Suricata remains IDS-only.
- Complete child destination allowlisting.
- Centralized SIEM/Wazuh production coverage.
- Independent audit of the deployment.

## 6. Visibility and interpretation limits

- Encrypted sessions do not reveal full application payloads without explicit decryption, which is not part of the current deployment.
- Domain blocking is not equivalent to telemetry elimination; applications can use cached answers, hardcoded IPs or encrypted transports.
- WAN IDS sees the traffic available on WAN and may have reduced attribution after NAT.
- Zenarmor classification depends on observable protocol/application characteristics and should be validated against expected service behavior.
- A detection alert is evidence to investigate, not automatic proof of compromise.

## 7. Evidence standard

Baseline segmentation and service checks are owner-reported. New controls should be promoted from "configured" to "validated" only after positive and negative tests, functional regression checks, timestamped results, and sanitized evidence using [validation-evidence-template.md](validation-evidence-template.md).

Do not disclose private addresses, SSIDs, device identifiers, raw packet captures, credentials, VPN keys, or unsanitized configuration/policy exports.
