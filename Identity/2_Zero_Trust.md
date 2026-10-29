# Zero Trust

[← Identity](./README.md)

**Zero Trust** means: do not treat network location as proof of trust. Authenticate and authorize every session and request; assume breach.

## Core ideas (program)

| Principle | Practice |
|-----------|----------|
| Verify explicitly | Strong identity (user + device + workload) on each access |
| Least privilege | Narrow roles; just-in-time elevation; short sessions |
| Assume breach | Segment; encrypt; monitor; limit lateral movement |

Zero Trust is a **strategy**, not a single product. Buying a ZTNA box without identity hygiene is theater.

## Mapping to this series

| Slice | Home |
|-------|------|
| User/app federation | [OAuth/OIDC/SAML](./1_OAuth_OIDC_SAML.md) |
| Design trade-offs (tokens, mTLS, gatekeeper) | [System-Design Auth vs ZT](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/2_Auth_Design_vs_Zero_Trust.md) |
| Wire / mesh | Networks Service-Mesh / Security |
| Cloud posture | [Cloud-Security](../Cloud-Security/README.md) |

## Anti-patterns

| Anti-pattern | Better |
|--------------|--------|
| “VPN means trusted” | VPN + still verify app identity and device posture |
| Flat service mesh with one identity | Per-workload identity and policy |
| Forever sessions | Re-auth on risk; revoke path that works |

## Next

- [Cloud-Security](../Cloud-Security/README.md) · [AppSec](../AppSec/README.md)
