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
- [ ] Re-validate the already established travel-router WireGuard IPv4 tunnel, including external-network interruption behavior, fail-closed routing, DNS/IPv6 leakage, and captive-portal restrictions (see [travel-router case study](secure-travel-router.md)).
- [ ] Treat any other home WireGuard client/access path as a separate workstream; do not infer that its deployment is verified from the travel-router tunnel.

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
# Hardening work queue — proposed changes, not the current deployment

**Planning snapshot: 2026-10-24.** All unchecked tasks are proposed/unfinished. This is an engineering backlog, not an implementation or validation record. Updates should specify completion date and link a sanitized test result before changing a checkbox to `[x]`.

## Phase 0 — verify baseline and create rollback capability (highest priority)

- [ ] Confirm OPNsense, switch, AP and Pi-hole are on supported firmware; record versions privately.
- [ ] Export and securely store an **offline** OPNsense backup (do not upload it to GitHub); verify that rollback is possible.
- [ ] Review the *actual* IoT rules for broad LAN/internet allows, IPv4/IPv6 equivalence, alias scopes, and intended enforcement order. Do **not** delete the broad IoT internet rule blindly: identify service dependencies first.
- [ ] Re-run segmentation tests on guest, IoT, cameras, management, and trusted zones; record a dated, sanitized test ledger.
- [ ] Confirm IoT clients can reach the approved Pi-hole resolver on port 53, while direct outbound DNS port 53 and DoT port 853 are denied.
- [ ] Probe controlled DoH and QUIC examples to establish whether encrypted DNS or telemetry remains possible; expect that current DNS port rules alone do not block them.
- [ ] Validate equivalent IPv6 behavior; verify no unexpectedly permitted resolver or management path on IPv6.

**Acceptance:** expected allow/deny outcomes documented, no regression of essential DNS/streaming, restoration tested, and remaining bypasses explicitly stated.

## Phase 1 — targeted IoT/TV visibility (observe before enforcing)

- [ ] Identify the LG TV using a local firewall alias/reservation privately, without disclosing the identifier publicly.
- [ ] Build a time-bounded, minimally invasive DNS/firewall metadata observation plan; log volume and retention controlled.
- [ ] Establish a functional baseline: streaming, updates, login, discovery/casting and apps used by the household.
- [ ] Correlate DNS answers with observed destinations and distinguish required service traffic from potential telemetry; do not assume all vendor traffic is unnecessary.
- [ ] Evaluate **Zenarmor on the IoT interface only** for application classification, encrypted-DNS identification and controlled restrictions. Check compatibility and resource/throughput impact first.
- [ ] Test a limited QUIC UDP/443 policy where useful. Record the fallback behavior and regressions instead of blanket-blocking as a first step.
- [ ] Develop a narrowly scoped TV egress policy using observed required destinations; stage, test and maintain a rollback path.
- [ ] Re-test streaming, firmware updates and casting after each policy change, then document residual DoH/telemetry uncertainty.

**Acceptance:** defined test window, at least one before/after flow comparison, no unsupported guarantee that all ACR or telemetry has stopped, and a documented fallback if functionality fails.

## Phase 2 — passive detection infrastructure (additive to OPNsense)

- [ ] Validate whether the managed Omada switch can mirror the chosen gateway-connected port in the actual firmware/configuration and verify what traffic is visible.
- [ ] Select a compact x86 host (e.g., an appropriately equipped used business mini-PC) for a passive sensor. **Do not replace the current firewall.**
- [ ] Use a separate management interface on the appropriate administration zone; connect a dedicated **capture NIC with no IP/gateway/bridge** to the mirror destination.
- [ ] Deploy and tune Suricata initially in **IDS-only** mode; start with alert review and false-positive reduction, not inline blocking.
- [ ] Add Zeek network metadata/protocol logs and explicit retention limits only if hardware throughput and storage permit it.
- [ ] Conduct a safe controlled traffic exercise and confirm sensor coverage, timestamps, loss statistics and alert/event interpretation.

**Acceptance:** capture only on authorized mirror traffic, no unexpected service exposure on sniffing interface, packet loss measured, relevant test traffic observed and repeatably documented.

## Phase 3 — centralized operations, management, and remote access

- [ ] Forward scoped OPNsense and sensor logs to a protected log collector; define retention, backups and access controls.
- [ ] Evaluate Wazuh only after realistic CPU/RAM/storage sizing and log-volume estimates; it is not currently deployed.
- [ ] Reduce management attack surface: dedicated admin network, MFA where supported, no external management access, patching, credential/key hygiene and documented exception review.
- [ ] Verify/implement home WireGuard remote access as a distinct project; do not confuse it with the previously discussed hardened travel router.
- [ ] Finish and test child-device destination allowlisting using both permitted and denied cases.
- [ ] Confirm the actual Unbound/Pi-hole relationship and explicitly record any configuration changes.

**Acceptance:** attributable and time-consistent logs, reviewed administrative access controls, fail-closed network access where intended, and documented rollback.

## Change record template

| Date | Change ID | Control / scope | Expected effect | Regression checks | Observed outcome | Rollback | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
| YYYY-MM-DD | CHG-XXX | *planned example only* | *to be tested* | *test IDs* | **NOT RUN** | *revert procedure* | *none* |

## Rules for promoting a proposal to a deployed claim

1. Confirm actual configuration on the running device.
2. Capture the policy/rule or package revision **privately**.
3. Perform allow-path and deny-path tests, including IPv6 where applicable.
4. Observe expected behavior and relevant regressions.
5. Sanitize and add an evidence record; link it from the current-state document.
6. Update README only after the control is configured and the result is established.

## Interview / portfolio framing

Useful research write-up: state the hypothesis (e.g. traditional DNS blocks may not catch DoH), build a repeatable experiment, report measured results and counterexamples, apply one narrowly scoped mitigation, retest, and disclose limitations. This is more credible than a claim of comprehensive protection without evidence.
