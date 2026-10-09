# Architecture decision record

## ADR-001 — Dedicated security gateway

**Decision:** Use a dedicated OPNsense firewall for central routing and inter-zone security policy.

**Rationale:** Supports rule visibility and administration separate from consumer-router features. **Tradeoff:** Added operational complexity and a critical network dependency.

## ADR-002 — Separate device classes into zones

**Decision:** Assign management, trusted, IoT, cameras, guest and child devices to purpose-specific logical networks.

**Rationale:** Reduces unnecessary default reachability between device classes. **Tradeoff:** Device discovery, casting and other convenience functions may require explicit exceptions.

## ADR-003 — Central DNS filtering

**Decision:** Use Pi-hole as an approved DNS filtering service.

**Rationale:** Provides central visibility and filtering. **Tradeoff:** DNS availability becomes operationally significant; encrypted DNS bypass requires additional design review.

## ADR-004 — Public engineering documentation without live configuration

**Decision:** Publish logical views, sanitized examples, and verification methodology, not production configuration exports.

**Rationale:** Shows engineering practice without exposing exploitable implementation details. **Tradeoff:** Public reviewers cannot directly replay exact system configuration.
