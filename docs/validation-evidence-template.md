# Sanitized validation evidence template

This template is for recording *future* or repeated tests. It is not evidence that any test was executed.

```
Test ID: NET-YYYY-XXX
Date (UTC): YYYY-MM-DD
Policy revision: anonymized change identifier (not a full export)
Objective: Deny a lower-trust client access to an authorized test service
Source zone: IOT (fictional label)
Destination zone/service: TRUSTED / dummy test service
Transport: TCP/UDP/etc.
Expected: DENY
Observed: NOT RUN
Result: PENDING
Method: e.g., curl/nc/dig against *an endpoint owned by the operator*
Negative control: a permitted path also tested
IPv6 equivalent: NOT RUN / NOT APPLICABLE
Limitations: This result only covers the tested endpoints/protocols/time window
Evidence: sanitized excerpt or aggregate counter, checked for secrets
Rollback: policy revision/restore method stored privately
```

## Test families

- **Segmentation:** guest/IoT/cameras/child-to-management/trusted deny tests; intended management access allow test.
- **DNS:** approved Pi-hole port 53 allow, external port 53/853 deny, tested DoH and IPv6 behavior (potentially bypassing port controls).
- **IoT telemetry:** observed metadata before/after a narrowly scoped rule, including regressions; avoid claiming payload visibility in encrypted HTTPS.
- **Detection:** safe test flow observable by sensor, checked logs/alerts, and evidence of packet-loss assessment.
- **Remote access:** control-path reachability, tunnel down fail-closed, DNS leaks, and IPv6 behavior, when that workstream is established.

## Publishing precautions

Redact or synthesize exact addresses, device identifiers, domain names that may expose private behavior, hostnames, internal rule aliases, full captures, cookies, session tokens, VPN materials and configuration exports. Prefer a textual test report over raw packet captures or logs.

**Evidence statuses:** `PENDING` (not executed), `PASS (owner-reported)` (executed but not independently substantiated), `PASS (sanitized evidence attached)`, `FAIL`, and `BLOCKED` (prerequisite not met). None of these are a third-party security certification.
