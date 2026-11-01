# Privileged access and IGA

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**PAM (privileged access management)** constrains standing admin power. **IGA (identity governance and administration)** proves the *right* people (and machines) have the *right* access over time — certifications, SoD (segregation of duties), and joiner–mover–leaver beyond the IdP login screen.

## Concept

### Why “SSO + MFA” still fails privileged paths

| Gap | Outcome |
|-----|---------|
| Standing Domain Admin / `root` | One phish → estate-wide blast |
| Shared break-glass passwords in vaults-of-spreadsheets | No attribution |
| Cloud Owner roles forever | Quiet data exfil |
| Contractors in admin groups after project end | Orphan privilege |

PAM shrinks **when** and **how** privilege is used. IGA shrinks **who** should have it and proves reviews happened.

### PAM building blocks

| Control | Intent |
|---------|--------|
| **Vault / check-out** | No standing knowledge of privileged passwords |
| **Session broker / recording** | Attribute actions; forensic replay |
| **JIT (just-in-time) elevation** | Time-bounded admin |
| **Endpoint privilege management** | Least privilege on workstations (no local admin forever) |
| **Secrets for apps** | Prefer short-lived workload creds — [11](./11_Workload_And_Non_Human_Identity.md) |

Human PAM ≠ secrets engines for machines (Vault/OpenBao), though programs often buy both.

### IGA building blocks

| Control | Intent |
|---------|--------|
| **Access requests / approvals** | Workflow instead of ticket chaos |
| **Provisioning connectors / SCIM** | JML into apps — [13](./13_Lifecycle_SCIM_And_Directories.md) |
| **Access certification** | Periodic “does this person still need X?” |
| **SoD policies** | Conflicting entitlements blocked or flagged |
| **Role mining / birthright roles** | Reduce one-off entitlements |

## Advanced

### Privilege tiers (program language)

| Tier | Examples | Expectation |
|------|----------|-------------|
| Standard user | Email, wiki | MFA; normal AAL |
| Power user | Prod read, limited deploy | Stronger auth; shorter sessions |
| Privileged | Cloud Owner, DC admin, IdP admin | JIT + PAM + monitoring |
| Break-glass | Offline emergency | Sealed process; always audited |

IdP admin and help-desk reset paths are **privileged** even when the UI looks like “support.”

### SoD examples

| Conflict | Why |
|----------|-----|
| Request access + approve same access | Fraud |
| Developer + production change approver without compensating control | Silent ship of malware |
| Finance posting + finance releasing | Classic ERP SoD |

### Detection and governance meet ITDR

Privileged session anomalies, impossible admin travel, and sudden entitlement grants belong in identity threat detection — [12](./12_Identity_Threats_Detect_And_Harden.md) — and SOC workflows in [Defensive-Ops](../Defensive-Ops/README.md).

### Tooling map

OSS and commercial PAM/IGA/IdP options: [16](./16_Identity_Tooling_Map.md). Install homes for OSS platforms live under [Tooling Security/Identity](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity).

## Apply

1. List standing human privileged roles (cloud, IdP, AD, CI, SaaS).  
2. Mark each: vaulted, JIT, or standing.  
3. Pick one standing role to convert to JIT this quarter.  
4. Schedule a certification for one high-risk app’s entitlements.  
5. Write the break-glass procedure (who, dual control, post-use review).

## Next

- [Identity tooling map](./16_Identity_Tooling_Map.md)

## Further reading

- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [CISA Zero Trust Maturity Model](https://www.cisa.gov/resources-tools/resources/zero-trust-maturity-model)
- [OMB M-22-09](https://www.whitehouse.gov/wp-content/uploads/2022/01/M-22-09.pdf)
- [RFC 7644 — SCIM Protocol](https://www.rfc-editor.org/rfc/rfc7644)
