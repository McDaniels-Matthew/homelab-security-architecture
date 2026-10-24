# Hardened travel router — remote WireGuard access to home

**Documented deployment:** GL.iNet Beryl AX (GL-MT3000), based on OpenWrt 24.10.4 as last recorded on **2026-10-02**. This is a public-safe, operator-reported engineering case study: it documents tested behavior, not an independently audited production VPN or a guarantee of secure operation on every network.

## Why it belongs in this architecture

The travel router extends the home-lab trust model beyond the physical house. Personal devices connect to a trusted or guest Wi-Fi network on the travel router; the router establishes a **WireGuard IPv4 full-tunnel connection to the home OPNsense gateway**. Home OPNsense then enforces network policy and provides access to the approved **Pi-hole DNS resolver** for the tunneled path.

This is a **router-to-home VPN connection**, not a claim that arbitrary external WireGuard clients, mobile devices, or every IPv6 path have been set up and tested.

## Sanitized topology

```text
Personal device(s)                 Untrusted hotel / public network
       |                                        |
       v                                        |
Travel router: trusted + isolated guest zones   |
       | WAN/uplink                             |
       +----------------------------------------+
       | Encrypted WireGuard tunnel (IPv4)
       v
Home OPNsense VPN endpoint
       |
       +-- Home firewall policy / zone isolation
       +-- Approved Pi-hole DNS path
       +-- Permitted onward Internet access (NAT)
```

The topology omits WAN/public IPs, hostnames, tunnel addresses, local prefixes, Wi-Fi names, peer public keys, and any other production identifier.

## Controls present at last validation

| Control / behavior | Reported state | Evidence boundary |
| --- | --- | --- |
| GL-MT3000 running OpenWrt 24.10.4 | **Deployed as of 2026-10-02** | Version may need re-verification after upgrades |
| Router management via SSH public-key authentication | **Configured** | Password-based SSH login disabled; administrative access limited to the trusted LAN |
| Management access from WAN and isolated guest | **Blocked in reported checks** | Applies to tested management paths |
| Trusted and separately isolated guest Wi-Fi | **Configured** | Guest-to-management denial reported; implementation deliberately sanitized |
| Unneeded service exposure | **Reduced** | Avahi disabled and unused IPsec rules disabled |
| Firewall invalid-state packets | **Dropped** | Packet-offloading disabled for troubleshooting/policy consistency |
| WireGuard client to home OPNsense | **Established, owner-validated on 2026-10-02** | Handshake and bidirectional payload traffic validated |
| IPv4 full-tunnel forwarding through OPNsense | **Owner-validated** | Home-side forwarding/NAT demonstrated in controlled test |
| Pi-hole DNS through tunnel | **Owner-validated in tunnel-up test** | DNS-bypass scenarios remain unverified |
| Home-network zone isolation on VPN path | **Owner-validated for selected tests** | Not equivalent to universal LAN isolation proof |

## Explicitly unresolved

- **External-network kill switch / fail-closed behavior:** a field interruption test of tunnel-down routing and potential direct-WAN fallback remains pending; do not claim protection against all tunnel outages.
- **DNS leaks and DNS-over-HTTPS/DoT bypass:** only the successful approved DNS path is recorded. Negative/bypass tests have not been comprehensively documented.
- **IPv6 leakage and fail-closed policy:** not yet shown to be prevented during tunnel operation or outage.
- **Real travel-network field testing:** captive portals, restrictive Wi-Fi, blocked UDP, unstable uplinks, and roaming across providers may prevent operation. "Works from anywhere" is a **design goal**, not a universal availability guarantee.
- **Version/cipher/key rotation and unattended recovery:** future verification/documentation required.
- **Independent evidence:** sanitized dated packet traces, tunnel tests, and configuration revisions are not publicly attached.

## Risk model

| Threat | Current mitigation | Residual exposure |
| --- | --- | --- |
| Untrusted access-point operator | Encrypted WireGuard tunnel for validated IPv4 traffic | Fallback/leak paths when tunnel drops not yet tested |
| Remote management attempts | SSH key-only and WAN/guest management restrictions | Firmware vulnerabilities, misconfigurations, new services |
| Guest device lateral movement | Separate guest zone, narrow reachability | Repeat cross-zone and IPv6 tests after changes |
| Unapproved DNS resolution | Working Pi-hole resolver path through VPN | DoH, hardcoded resolvers, tunnel-down behavior unverified |
| Lost device | Reduced administrative exposure | Physical compromise and credential revocation procedure remain important |
| Tunnel peer broad home access | Home OPNsense zone policy | Review rule exceptions and allowed sources each change |

## Validation backlog — not yet complete

- [ ] Verify actual firmware/software revisions and key lifecycle privately.
- [ ] Re-run trusted-to-guest and guest-to-management isolation cases with both allow and deny controls.
- [ ] Perform an **external-network** WireGuard interruption test: expected fail-closed behavior, observed route/DNS behavior, and documented rollback.
- [ ] Test IPv4 direct-WAN fallback and DNS leakage during tunnel-up and tunnel-down states.
- [ ] Repeat with IPv6-capable uplinks and explicitly document handling of IPv6 default routes and name resolution.
- [ ] Test public captive portal and restricted outbound UDP scenarios without weakening the intended security policy.
- [ ] Generate dated, sanitized evidence using [the shared evidence template](validation-evidence-template.md).
- [ ] Re-validate home-side firewall isolation and DNS after OPNsense policy changes.

See [hardening roadmap](hardening-roadmap.md) for sequencing. Treat any unverified claim as a hypothesis until measured.

## Disclosure boundary

No production configuration export, keys, certificates, MAC/IP addresses, SSIDs, passwords, hostnames, serial numbers, packet captures, or unique network identifiers should be committed. Keep rollback backups and raw test logs encrypted and private.
