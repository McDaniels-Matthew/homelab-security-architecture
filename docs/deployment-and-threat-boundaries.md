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
