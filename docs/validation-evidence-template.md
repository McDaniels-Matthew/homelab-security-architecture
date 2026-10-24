# Sanitized validation evidence template

This page is a reusable reporting template, **not evidence that tests have been run**.

## Example test record (all placeholders)

    Test ID: NET-YYYY-XXX
    Date (UTC): YYYY-MM-DD
    Change / rule revision: sanitized label only
    Objective: Block lower-trust client from management test service
    Source zone: IOT (fictional name)
    Destination: MANAGEMENT / synthetic test host
    Protocol: TCP
    Expected result: DENY
    Actual observation: NOT RUN
    Outcome: PENDING
    Procedure: command against an endpoint controlled by the operator
    Positive control: expected allowed path also tested
    Negative control: expected blocked path also tested
    IPv6 equivalent: NOT RUN / NOT APPLICABLE
    Limitations: Applies only to recorded endpoints, protocols and test time
    Evidence: redacted excerpt or counters; no raw personal data
    Rollback: private backup / revision label

## Test families

- **Segmentation:** lower-trust → trusted/management denied; approved administrator → management allowed.
- **DNS:** approved Pi-hole 53 allowed, external 53/853 blocked, independently test DoH/QUIC and IPv6 behavior.
- **TV/IoT:** baseline streaming functionality and DNS/firewall metadata before and after a narrow egress policy.
- **Detection:** validate mirrored-port traffic coverage, sensor timestamps and loss counters, and test alerts.
- **Remote access:** verify tunnel-up access, tunnel-down behavior, DNS handling, and IPv6 once configured.

## Evidence status terminology

- **PENDING:** not executed.
- **PASS (owner-reported):** test executed but public evidence not yet attached.
- **PASS (sanitized evidence attached):** a dated, scope-limited test report is available.
- **FAIL:** observed behavior contradicts expectation.
- **BLOCKED:** prerequisite absent; test could not be run.

No single test constitutes independent certification or a claim of perfect isolation.

## Publication safeguards

Remove or synthesize internal addressing, SSIDs, real aliases, host/device names, serial numbers, MACs, personal telemetry domains, credentials, session tokens, VPN materials, raw packet captures and production configuration exports. Keep originals in a separately secured private location.
