# Segmentation and security validation results

**Status:** The network owner reports that all eleven listed checks passed in their home lab. Results are self-reported; dated commands, log excerpts, and policy revision details have not yet been independently documented in this public repository. Test scope and implementation details may evolve.

| ID | Test source | Test objective | Expected outcome | Result |
|---|---|---|---|---|
| SEG-01 | Guest | Reach firewall admin interface | Block | Passed (owner-reported) |
| SEG-02 | Guest | Reach trusted-zone test host | Block | Passed (owner-reported) |
| SEG-03 | Guest | Reach public HTTPS test endpoint | Allow | Passed (owner-reported) |
| SEG-04 | IoT | Reach trusted-zone test host | Block | Passed (owner-reported) |
| SEG-05 | Cameras | Reach management interface | Block | Passed (owner-reported) |
| SEG-06 | Trusted | Reach approved DNS service | Allow | Passed (owner-reported) |
| SEG-07 | Trusted | Query an unauthorized plain-DNS destination | Block/redirect per verified design | Passed (owner-reported) |
| SEG-08 | Child | Reach disallowed external site | Block for tested destination (complete allowlist scope unverified) | Passed (owner-reported) |
| SEG-09 | Non-admin | Reach switch/AP administration | Block | Passed (owner-reported) |
| SEG-10 | Appropriate admin source | Reach approved management interface | Allow | Passed (owner-reported) |
| SEG-11 | Any applicable zone | Attempt same paths over IPv6 | Match intended IPv6 policy | Passed (owner-reported) |

## Test method

1. Use a lab endpoint assigned to the intended zone and confirm its VLAN association.
2. Select a **controlled test destination** under your administration; do not scan unrelated networks.
3. Attempt the intended protocol (ICMP alone does not prove application reachability).
4. Inspect relevant firewall logs for matching traffic, taking care not to publish identifying addresses.
5. Capture outcome, date, firmware/policy revision and unexpected behavior in a **private** test log.
6. Publish only aggregated, sanitized evidence after reviewing it for secrets and network identifiers.

## Example public evidence record (template)

```text
Test ID: SEG-01
Test date: ADD VERIFIED DATE
Observed result: PASS (owner-reported; detailed evidence pending)
Expected result: BLOCK
Policy revision: REDACTED / NOT RECORDED
Notes: Add sanitized supporting evidence and tested configuration scope.
```
