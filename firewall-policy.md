# High-level firewall policy

This is a conceptual policy matrix. It is **not** a copy of the running OPNsense rule set and contains no sensitive addresses or identifiers.

Legend: **Allow** = intended policy, **Deny** = default policy, **Restricted** = narrowly scoped exceptions, **Pending** = not fully implemented/validated.

| Source | WAN / internet | Management | Trusted | IoT | Cameras | Guest |
|---|---|---|---|---|---|---|
| Management | Restricted | Restricted | Restricted | Restricted | Restricted | Deny |
| Trusted | Allow | Restricted | — | Restricted | Restricted | Deny |
| IoT | Restricted | Deny | Deny | — | Deny | Deny |
| Cameras | Restricted | Deny | Deny | Deny | — | Deny |
| Guest | Allow | Deny | Deny | Deny | Deny | — |
| Child devices | Pending restrictions | Deny | Restricted | Deny | Deny | Deny |

## Policy design notes

- DHCP/DNS/NTP and captive portal traffic may require carefully scoped exceptions not shown in the matrix.
- Management-zone WAN access is intentionally described as restricted rather than unrestricted.
- Trusted-zone access to infrastructure should be based on explicit administrative needs.
- Camera internet access should be minimized to actual operational requirements.
- Child allowlisting remains **in progress**, not an implemented guarantee.
- A passing policy test should confirm **both allowed and denied paths**.

## Proposed change record template

| Date | Rule / purpose | Risk | Reviewer | Validation evidence | Rollback |
|---|---|---|---|---|---|
| YYYY-MM-DD | Example: permit a required DNS path | Low/medium/high | TBD | Test ID | Revert rule |
