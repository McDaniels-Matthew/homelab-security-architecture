# Deployment state and trust-boundary assessment

**Snapshot: 2026-10-24.** This document distinguishes reported deployment from planned improvements. Results described as passing are owner-reported, not independently audited.

## Assets and trust boundaries

- **Protect:** management interfaces, trusted household endpoints, camera resources, virtualization services, and DNS integrity.
- **Less trusted:** smart TVs and other IoT devices, guests, and unmanaged clients.
- **Boundaries:** WAN to gateway; IoT/guest/camera to trusted/management; clients to designated DNS; ordinary clients to management interfaces.
- **Threats in scope:** compromised IoT firmware, lateral movement, misconfigured firewall exceptions, unsolicited WAN access, DNS-policy bypass, and unnecessary telemetry.

## Current controls and residual risk

| Boundary | Reported present control | Residual exposure |
| --- | --- | --- |
| WAN → home | OPNsense perimeter policy limits unsolicited inbound access | Does not prevent device-initiated connections to Internet destinations |
| IoT/cameras → trusted or management | Separate VLANs and OPNsense inter-zone restrictions; relevant checks reported passed | Incorrect exceptions, IPv6 differences, and later policy changes can reopen paths |
| Guest → private resources | Internet-only guest policy and captive portal | Captive portal is not itself a substitute for firewall isolation |
| IoT → DNS | Pi-hole as approved port-53 resolver; external traditional DNS (53) and DNS-over-TLS (853) reportedly restricted | Does not ensure DNS-over-HTTPS (DoH) is blocked on HTTPS/443 |
| IoT → Internet | General outbound Internet allow remains present | Vendor/cloud telemetry and encrypted DNS may still be reachable |
| Smart TV → vendor services | Pi-hole blocking for known domains and basic egress policy | No verified complete ACR/telemetry suppression, destination allowlist, or decryption of HTTPS |

**Do not claim:** all DNS requests must traverse Pi-hole; all LG TV telemetry/ACR is blocked; encrypted HTTPS payloads are inspected; the home network has a production SIEM or IDS sensor; or the entire deployment has been independently security audited.

## Not implemented or not yet verified

- Targeted application-aware policy/Zenarmor evaluation on the IoT zone: **planned**, subject to performance and rollback testing.
- Encrypted DNS/QUIC bypass tests and restrictions: **not yet comprehensively verified**.
- Per-device LG TV outbound destination policy: **planned**, after observing legitimate services.
- Separate mini-PC network sensor, Omada port-mirroring check, Suricata IDS-only, optional Zeek, centralized logs/Wazuh: **planned**, not active detection coverage.
- Full child-device destination allowlisting: **in progress**, not complete.
- Pi-hole/Unbound integration and home WireGuard end-to-end behavior: **verify separately**, not assumed.
- Production TLS interception of TV traffic: **not part of current deployment**.

## Visibility limitations

A mirrored gateway uplink generally observes traffic that crosses that link; it may miss same-VLAN or locally switched wireless traffic. A proposed passive capture NIC should have **no assigned IP, gateway, bridge, or listening services**. Mirror oversubscription can drop traffic; inspect capture-loss statistics before relying on results.

Encrypted HTTPS cannot be equated with inspected application content. DoH, hardcoded destination addresses, local caches, certificate pinning, and QUIC can undermine assumptions based solely on DNS logs.

## Evidence standard

The existing [validation tests](validation-tests.md) describe **11 passing, owner-reported checks**. They are not publicly independently reproducible yet. Use the [sanitized validation template](validation-evidence-template.md) for dated, scoped, repeatable tests including positive and negative controls, relevant IPv6 behavior, rule revision, and rollback.

Do not disclose private addresses, SSIDs, device identifiers, raw packet captures, credentials, or unsanitized configuration backups.
**Snapshot: 2026-10-24.** Purpose: describe the current installation without converting proposed security features into completion claims. All results are owner-reported unless accompanied by sanitized verification evidence.

## 1. Authorized functionality and assets

- **Protect:** management interfaces, trusted clients, household data, camera network, and DNS policy.
- **Less trusted:** smart TVs and other IoT endpoints, guests, and unmanaged client devices.
- **Trust boundaries:** WAN ↔ firewall; IoT ↔ trusted/management; guest ↔ private resources; endpoint ↔ approved DNS; client ↔ management interface.
- **Threats in scope:** lateral movement, vulnerable IoT firmware, external exposure, unapproved DNS resolvers, telemetry beyond functional need, and encrypted traffic that evades domain-based policies.

## 2. Current deployed controls (owner reported)

| Boundary | Current control | What is established | Residual exposure |
| --- | --- | --- | --- |
| WAN → LAN/TV | State-aware inbound deny / no unsolicited inbound access | Unsolicited WAN reachability limited per intended policy | Does not restrict device-initiated outbound sessions |
| IoT → trusted/management | VLAN separation and firewall policy | Owner-reported access tests passed | New exceptions/misconfiguration can reopen paths |
| Guest → private network | Guest policy; captive portal for internet use | Owner-reported baseline access tests passed | Portal alone does not enforce all interzone restrictions |
| IoT → approved DNS | Pi-hole port 53 permitted; external DNS 53 and DoT 853 denied | Blocks specified protocols and destinations | DoH uses HTTPS; application-level tunnels remain possible |
| IoT → internet | General Internet egress still permitted | Devices remain functional | HTTPS vendor telemetry and DoH can egress |
| TV services → vendor cloud | Pi-hole DNS policy and basic egress controls | May block known domains at DNS resolution | Not a verified block of ACR, DoH, QUIC, or embedded IP-based services |

**Do not claim:** that the firewall decrypts smart-TV HTTPS, that all DNS is forced through Pi-hole, that TV ACR/telemetry is entirely prevented, or that a live intrusion detection stack is monitoring all traffic.

## 3. Incomplete or not yet verified controls

- **DoH / HTTPS bypass protection:** not comprehensive, even if traditional DNS and DoT port restrictions are in effect.
- **UDP/443 (QUIC) controls:** discussed but not validated; blanket blocks may affect streaming or app behavior.
- **Strict smart-TV destinations:** per-device egress allowlisting/denylisting and monitoring are future work.
- **Zenarmor application visibility and policy:** proposed IoT-limited evaluation, with compatibility, throughput and rollback requirements.
- **Suricata, Zeek, Wazuh, centralized syslog:** not claimed running; deployment topology and resource sizing to be verified.
- **SPAN / mirror-port passive sensor:** proposed additional mini-PC sensor using switch port mirroring; not a replacement for OPNsense.
- **WireGuard and access hardening:** separate or partially evaluated workstreams; no general assertion of home VPN deployment.
- **Child-device allowlisting and Unbound integration:** not established as fully deployed/validated.

## 4. Known visibility limits

- A sensor on a switch-mirrored gateway uplink sees only frames crossing that monitored segment; intra-VLAN or some wireless-client traffic may never traverse it.
- A mirror destination and physical capture interface must be configured safely: **no IP address, default route, bridge, or listening services** on the passive capture NIC.
- Mirror-port oversubscription may cause packet drops; verify capture loss before drawing conclusions.
- Encrypted sessions do not reveal the full application payload without explicit decryption, which is not part of the current network.
- DNS blocking is not equal to telemetry elimination: some devices cache resolutions, use hardcoded IPs or run DoH.

## 5. Engineering claims and evidence

Eleven existing baseline checks are marked **passed (owner-reported)** in [validation-tests.md](validation-tests.md). Dated source and destination labels, protocol, expected result, observed result, firmware/rule revision, failure modes, and negative controls should be added using [validation-evidence-template.md](validation-evidence-template.md).

Use public fictional network labels (not real VLAN IDs, SSIDs, aliases or IPs) in any published evidence. Keep raw captures privately secured; do not publish original logs.
