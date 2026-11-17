# Architecture decision record

## ADR-001 — Dedicated security gateway

**Decision:** Use a dedicated OPNsense firewall for central routing and inter-zone security policy.

**Rationale:** Supports explicit, reviewable trust-boundary enforcement separate from consumer-router features. **Tradeoff:** Added operational complexity and a critical network dependency.

## ADR-002 — Separate device classes into zones

**Decision:** Assign management, trusted, IoT, cameras, guest, lab and child devices to purpose-specific logical networks.

**Rationale:** Reduces unnecessary default reachability between device classes. **Tradeoff:** Device discovery, casting and convenience functions may require explicit exceptions.

## ADR-003 — Central DNS filtering

**Decision:** Use Pi-hole with local recursive resolution as the approved DNS service for controlled clients.

**Rationale:** Provides central visibility, filtering and resolver control. **Tradeoff:** DNS availability becomes operationally significant; encrypted-DNS bypass requires additional controls.

## ADR-004 — Public engineering documentation without live configuration

**Decision:** Publish logical views, sanitized examples and verification methodology, not production firewall, Zenarmor, DHCP or VPN exports.

**Rationale:** Demonstrates engineering practice without exposing directly reusable implementation details. **Tradeoff:** Public reviewers cannot replay the exact system configuration.

## ADR-005 — Layered L3/L4 and L7 enforcement

**Decision:** Keep OPNsense firewall policy authoritative for segmentation while using Zenarmor on selected internal networks for application-aware controls.

**Rationale:** Firewall rules express stable trust boundaries; Zenarmor adds visibility and controls for encrypted-DNS applications, VPN/proxy circumvention, malicious categories and other L7 behavior. **Tradeoff:** Policy matching and inspection placement must be validated to avoid unexpected fallback behavior or duplicate enforcement.

## ADR-006 — Subnet-scoped Zenarmor custom policies

**Decision:** Match restricted-zone Zenarmor policies by their source subnets rather than relying solely on VLAN-ID criteria.

**Rationale:** Testing showed custom policies matched reliably with subnet-only criteria on the selected logical VLAN inspection interfaces. **Tradeoff:** Addressing and policy definitions must remain synchronized if subnets are later renumbered.

## ADR-007 — Suricata as WAN IDS before IPS

**Decision:** Run Suricata on WAN in PCAP live-mode IDS first, using a curated ET Open rule set and structured logging; do not immediately enable inline drop actions.

**Rationale:** Establishes a detection baseline and false-positive profile without risking service disruption. **Tradeoff:** IDS observes and alerts but does not prevent matched traffic until selected signatures are deliberately promoted to IPS/drop behavior.

## ADR-008 — Separate responsibilities among security layers

**Decision:** Use OPNsense for routing/segmentation, Pi-hole for DNS filtering, Zenarmor for internal L7 policy, and Suricata for WAN signature detection.

**Rationale:** Avoids treating overlapping tools as interchangeable and keeps each control tied to a clear threat-model function. **Tradeoff:** Troubleshooting requires understanding which layer made a decision.
