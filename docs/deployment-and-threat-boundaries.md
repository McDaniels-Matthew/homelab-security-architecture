# Deployment state and trust-boundary assessment

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
