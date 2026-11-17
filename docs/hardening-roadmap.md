# Hardening work queue

**Planning snapshot: 2026-11-16.** Completed checkboxes mean the control has been configured or deployed. A completed checkbox does **not** imply independent validation unless the item explicitly says so. Dated sanitized regression evidence remains a separate requirement.

## Phase 0 — baseline and safe rollback

- [x] Deploy OPNsense, managed switching/AP infrastructure and Pi-hole on the segmented network.
- [x] Establish dedicated trusted, IoT, camera, guest, lab and child network classes.
- [x] Configure Pi-hole as the approved resolver and enforce traditional DNS/DoT restrictions on restricted zones.
- [ ] Publish dated sanitized regression evidence for the major allow/deny paths after the latest inspection-policy changes.
- [ ] Verify equivalent IPv6 behavior or explicitly disable/contain IPv6 until equivalent policy is implemented.
- [ ] Verify current offline OPNsense backup and restoration procedure; never commit production backups.
- [ ] Record current supported firmware/software versions privately.

**Acceptance:** recovery path proven; current segmentation/DNS tests documented; IPv6 behavior explicitly known.

## Phase 1 — IoT / smart-TV egress hardening

- [x] Identify the smart TV privately with a stable local reservation/alias.
- [x] Capture DNS/firewall metadata and establish a functional service baseline.
- [x] Add smart-TV-specific Pi-hole filtering and region-aware regex controls.
- [x] Deploy Zenarmor on the IoT inspection path.
- [x] Configure an IoT-restricted policy with encrypted-DNS, VPN/proxy, malicious-destination and DNS-anomaly controls.
- [x] Confirm subnet-only Zenarmor matching correctly selects the intended IoT policy.
- [ ] Complete regression testing for required streaming, firmware/update, account-login and local-discovery behavior after policy changes.
- [ ] Continue identifying unnecessary vendor telemetry and narrow it only when required functions remain intact.
- [ ] Test DoH/DoQ/QUIC bypass cases explicitly rather than inferring success from port-based rules.

**Acceptance:** required services function; before/after behavior recorded; residual telemetry uncertainty stated honestly.

## Phase 2 — broader application-aware policy

- [x] Add Zenarmor inspection to the intended restricted VLANs.
- [x] Create distinct restricted policies for IoT, cameras, guest and child zones.
- [x] Apply a security-oriented Default fallback policy to unmatched inspected traffic.
- [x] Use subnet matching for restricted-zone policies after validating that VLAN-only criteria did not match reliably in the chosen inspection placement.
- [ ] Regression-test trusted, IoT, camera, guest and child workflows after final interface selection.
- [ ] Export sanitized policy summaries/diffs for portfolio evidence without publishing live configuration.

**Acceptance:** each test client maps to the intended policy, expected applications work, and blocked cases are reproducible.

## Phase 3 — Suricata detection baseline

- [x] Enable Suricata through OPNsense Intrusion Detection.
- [x] Configure WAN-only PCAP live mode (IDS / alert-only).
- [x] Use Hyperscan and enable syslog/EVE outputs.
- [x] Download a curated high-value subset of Emerging Threats Open rules.
- [ ] Collect a representative alert baseline under normal household use.
- [ ] Classify noisy/low-value signatures and document any disables/suppressions.
- [ ] Generate safe controlled test traffic to prove the IDS sees expected events.
- [ ] Review CPU/memory/throughput impact under realistic load.
- [ ] Promote only high-confidence, low-false-positive signatures to IPS/drop after baselining.
- [ ] Re-run connectivity/regression tests after any move to inline IPS.

**Acceptance:** alert quality understood, false positives documented, resource impact acceptable, and any blocking actions supported by evidence.

## Phase 4 — logging, remote access and residual hardening

- [ ] Forward selected firewall/Zenarmor/Suricata logs to a protected collector with defined retention and access controls.
- [ ] Evaluate Wazuh/SIEM after measuring log volume and resource requirements.
- [ ] Audit management-plane exposure, patching, administrative exceptions and credential/key hygiene.
- [ ] Complete child-device destination allowlisting if the requirement remains necessary beyond current L7/content controls.
- [ ] Re-test the travel-router WireGuard tunnel for fail-closed behavior, DNS/IPv6 leakage and captive-portal scenarios.
- [ ] Revisit a dedicated passive SPAN/Zeek sensor only if it adds useful visibility beyond the current inline/IDS stack.

**Acceptance:** attributable logs, reviewed administrative access, remote-access failure modes understood, and no unsupported "fully secure" claims.

## Change record template

| Date | Change ID | Control / scope | Expected effect | Regression checks | Observed outcome | Rollback | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
| YYYY-MM-DD | CHG-XXX | *example* | *hypothesis* | *test IDs* | **NOT RUN** | *revert procedure* | *none* |

## Rules for promoting a configured control to a validated claim

1. Confirm the running configuration.
2. Retain a private revision/backup.
3. Perform allow-path and deny-path tests, including IPv6 where applicable.
4. Observe expected behavior and relevant regressions.
5. Sanitize and add an evidence record.
6. Update current-state documentation only to the level the evidence supports.

## Portfolio framing

Strong evidence follows: threat/hypothesis → control design → test method → measured findings → impact and limitations → controlled mitigation → regression results. Installing a product is not itself proof of security coverage.
