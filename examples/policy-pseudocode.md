# Illustrative firewall policy pseudocode

> This is documentation, **not executable OPNsense configuration**. Zone names and destinations below are fictional labels.

```text
# Evaluate explicit exceptions before baseline denies.
ALLOW management_admins -> network_device_admin_interfaces : required_management_protocols
ALLOW approved_clients -> approved_dns_resolver : DNS
ALLOW guest -> internet : approved_web_services
DENY  guest -> private_zones : ANY
DENY  iot -> management : ANY
DENY  cameras -> management : ANY
DENY  lower_trust_zones -> trusted : ANY
DENY  unsolicited_wan -> internal_zones : ANY
LOG   denied_cross_zone_attempts : rate_limited
```

Actual interface policy evaluation order, state handling, aliases, IPv6, required services, and platform-specific behavior must be reviewed and tested separately.
