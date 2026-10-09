# Segmentation and security validation plan

**Status:** These are proposed test cases. No tests below are represented as executed or passed. Record results only after performing them in the authorized home lab.

| ID | Test source | Test objective | Expected outcome | Result |
|---|---|---|---|---|
| SEG-01 | Guest | Reach firewall admin interface | Block | Not run / not documented |
| SEG-02 | Guest | Reach trusted-zone test host | Block | Not run / not documented |
| SEG-03 | Guest | Reach public HTTPS test endpoint | Allow | Not run / not documented |
| SEG-04 | IoT | Reach trusted-zone test host | Block | Not run / not documented |
| SEG-05 | Cameras | Reach management interface | Block | Not run / not documented |
| SEG-06 | Trusted | Reach approved DNS service | Allow | Not run / not documented |
| SEG-07 | Trusted | Query an unauthorized plain-DNS destination | Block/redirect per verified design | Not run / not documented |
| SEG-08 | Child | Reach disallowed external site | Desired: block; control pending | Not run / not documented |
| SEG-09 | Non-admin | Reach switch/AP administration | Block | Not run / not documented |
| SEG-10 | Appropriate admin source | Reach approved management interface | Allow | Not run / not documented |
| SEG-11 | Any applicable zone | Attempt same paths over IPv6 | Match intended IPv6 policy | Not run / not documented |

## Test method

1. Use a lab endpoint assigned to the intended zone and confirm its VLAN association.
2. Select a **controlled test destination** under your administration; do not scan unrelated networks.
3. Attempt the intended protocol (ICMP alone does not prove application reachability).
4. Inspect relevant firewall logs for matching traffic, taking care not to publish identifying addresses.
5. Capture outcome, date, firmware/policy revision and unexpected behavior in a **private** test log.
6. Publish only aggregated, sanitized evidence after reviewing it for secrets and network identifiers.

## Example evidence record (placeholder)

```text
Test ID: SEG-01
Test date: NOT RUN
Observed result: NOT RECORDED
Expected result: BLOCK
Policy revision: REDACTED / NOT RECORDED
Notes: Replace this example only after a real test.
```
