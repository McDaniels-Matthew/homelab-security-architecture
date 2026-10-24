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
