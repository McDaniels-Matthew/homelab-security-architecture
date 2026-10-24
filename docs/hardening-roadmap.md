# Hardening work queue — future changes, not current deployment

**Planning snapshot: 2026-10-24.** Every unchecked item is pending. Mark a task complete only after implementing it, repeating positive/negative tests, recording a date, and linking sanitized evidence. This page is a future-work reference; the [README](../README.md) describes the present deployment.

## Phase 0 — baseline and safe rollback

- [ ] Record OPNsense, Omada switch/AP, and Pi-hole software versions privately and confirm support/updates.
- [ ] Export an offline, encrypted OPNsense configuration backup; **never commit it**. Verify the restoration procedure.
- [ ] Review actual IoT policy order, aliases, broad WAN allow rules, per-zone exceptions, and IPv6 equivalents before making changes.
- [ ] Re-run all 11 baseline tests and publish dated sanitized outcome summaries. Do not assume previous passes remain valid after rule changes.
- [ ] Verify IoT → Pi-hole TCP/UDP 53 access and denial of external DNS 53 / DoT 853, for both intended IPv4 and IPv6 policies.
- [ ] Test whether controlled DoH over TCP/443 and QUIC over UDP/443 can bypass the intended DNS controls.
- [ ] Confirm essential streaming, casting, DNS, updates, and login flows before tightening any outbound policy.

**Acceptance:** recovery path tested; baseline passes documented; unsolved DNS/HTTPS bypasses listed without overclaiming.

## Phase 1 — observe and limit LG TV / IoT egress

- [ ] Identify the TV privately through firewall aliases or reservations (do not publish its identifiers).
- [ ] Capture minimum necessary DNS/firewall flow metadata during defined time windows, with strict log retention.
- [ ] Establish the real service baseline: streaming apps, firmware checks, account login, casting/device discovery.
- [ ] Compare requested domains and outbound connections with essential functionality; treat telemetry classification as a hypothesis until tested.
- [ ] Evaluate **Zenarmor on IoT only**, contingent on platform compatibility, throughput, resource cost, and rollback.
- [ ] Trial narrow DoH and/or UDP 443 (QUIC) restrictions on a test endpoint; document fallback and functionality loss.
- [ ] Build a narrowly scoped per-device allow/deny policy based on observed required services; do not blindly remove the current broad Internet allow.
- [ ] Re-test functionality and outbound traffic after each incremental rule change.

**Acceptance:** before/after measurements, working household services, and residual visibility limitations explicitly reported. DNS filtering alone is not proof of eliminating ACR/telemetry.

## Phase 2 — passive monitoring without replacing OPNsense

- [ ] Confirm whether the actual SG2008P switch firmware supports suitable port mirroring, then verify the target traffic is visible.
- [ ] Select a used x86 mini-PC (or other appropriately sized host), retaining OPNsense as the firewall.
- [ ] Separate management networking from packet capture; leave passive capture NIC without IP, routing, bridging, or exposed services.
- [ ] Install Suricata **IDS-only** first; confirm packet visibility and tune false positives before any IPS discussion.
- [ ] Evaluate Zeek protocol metadata if storage, performance, and privacy constraints permit.
- [ ] Generate benign controlled test traffic; verify timestamps, expected flow visibility, packet-loss counters, and alert interpretation.

**Acceptance:** reproducible observations without modifying traffic paths, measurable capture loss, and no unexpected management exposure.

## Phase 3 — security operations and residual hardening

- [ ] Forward selected firewall/sensor logs to a protected collector with access restrictions and retention limits.
- [ ] Size and evaluate Wazuh/SIEM only after estimating CPU, RAM, storage, and log volume.
- [ ] Audit management ACLs, exposed services, patching, MFA where available, keys, credentials, and administrative exceptions.
- [ ] Finish and positively/negatively test child-device destination allowlisting.
- [ ] Verify Pi-hole/Unbound relationship and assess DNS resilience.
- [ ] Implement and validate home WireGuard remote access only as a separately tested workstream; distinguish it from the hardened travel router.

**Acceptance:** scoped logs, confirmed rule behavior and exceptions, documented rollback, and no unjustified "fully secure" statements.

## Change record template

| Date | Change ID | Control | Expected | Regression tests | Observed | Rollback | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
| YYYY-MM-DD | CHG-XXX | *proposed example* | *hypothesis* | *test IDs* | **NOT RUN** | *local backup* | *none* |

## Promoting a proposal to current deployment

1. Verify the running configuration.
2. Retain a private revision/backup.
3. Test permitted and denied paths, including IPv6 where applicable.
4. Check functional regressions and negative controls.
5. Publish a **sanitized** evidence summary using [the evidence template](validation-evidence-template.md).
6. Then update the README and this checklist.

### Portfolio framing

Strong research evidence follows: hypothesis → test method → measured findings → impact and limitations → controlled mitigation → regression results. Do not equate installing a tool with obtaining monitoring coverage or comprehensive protection.
