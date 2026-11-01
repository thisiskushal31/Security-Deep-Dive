# Lifecycle, SCIM, and directories

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

Federation answers “how does this app learn who you are *today*?” **Lifecycle** answers “who exists, what they should have, and what happens when they leave.” Most identity breaches after “we have SSO” are orphan accounts, over-provisioned groups, and directories nobody owns.

## Concept

### Joiner–mover–leaver (JML)

| Event | Must happen |
|-------|-------------|
| **Joiner** | Create (or link) identity; assign birthright access; enroll authenticators |
| **Mover** | Change roles/groups; revoke old entitlements; re-certify sensitive access |
| **Leaver** | Disable/delete on schedule; revoke sessions/tokens/keys; remove from every SP |

SSO without JML automation creates permanent standing access. Offboarding runbooks: [Defensive-Ops/10](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md).

### Directories vs IdPs vs apps

| Store | Role |
|-------|------|
| **Directory** (LDAP / Active Directory / FreeIPA) | Source of users/groups; Kerberos for hosts; often HR-adjacent |
| **IdP / CIAM platform** | Authentication, MFA, federation protocols |
| **App local accounts** | Legacy; migrate toward federated + provisioned |

Workforce identity usually flows: HR → directory/IGA → IdP → apps. Customer identity (CIAM) often inverts: app/IdP first, weaker directory coupling.

### SCIM (System for Cross-domain Identity Management)

SCIM 2.0 (RFC 7643 schema + RFC 7644 protocol) is the common **HTTP/JSON provisioning** API for Users and Groups across domains (especially SaaS).

```text
HR / IGA / IdP ──SCIM──▶ SaaS (create/update/deactivate users & groups)
```

| Without SCIM | With SCIM (done well) |
|--------------|------------------------|
| Tickets + manual admin consoles | Automated create/update/disable |
| “Ghost” seats after leaver | Deprovision on authoritative signal |

SCIM is not magic: you still need an **authoritative source**, attribute mapping, and conflict rules.

## Advanced

### Authoritative source and conflict

| Pattern | Risk if wrong |
|---------|----------------|
| HR is truth for employment status | Contractors/vendors need a parallel source |
| Directory is truth for UIDs | Duplicate humans across forests |
| IdP is truth for MFA state | Recovery bypasses directory policy |

Write down *one* system allowed to create identities for each population (employees, contractors, bots, customers).

### Provisioning vs just-in-time (JIT) federation

| Approach | When |
|----------|------|
| **JIT** from assertion | First login creates local user — simple; weak for pre-provisioned access reviews |
| **SCIM / connector push** | App must know users before login; leaver disable without waiting for login |
| **Pull sync** | Batch jobs; latency and delete semantics matter |

### Directory hazards (program view)

| Hazard | Harden |
|--------|--------|
| Nested groups nobody understands | Flatten critical roles; review regularly |
| Service accounts with password-never-expires | Prefer workload identity — [11](./11_Workload_And_Non_Human_Identity.md) |
| Legacy NTLM / weak Kerberos options | Align with platform hardening guides |
| Help-desk reset as AAL downgrade | Recovery comparable to login — [3](./3_Authenticators_MFA_And_WebAuthn.md) |

IGA (identity governance and administration) sits above directories: certifications, SoD (segregation of duties), access requests — [15](./15_Privileged_Access_And_IGA.md).

### Tooling (install homes)

Directories, IdPs, and SCIM-capable platforms: [Identity tooling map](./16_Identity_Tooling_Map.md) → [Tooling Security/Identity](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity).

## Apply

1. Draw JML for employees and for non-humans separately.  
2. Name the authoritative source for “still employed?”  
3. List top 10 SaaS apps: SCIM, SAML-only, or manual?  
4. Pick one app with manual offboarding; design SCIM or IdP lifecycle for it.  
5. Measure orphan accounts (disabled in HR, active in IdP/SaaS).

## Next

- [Authorization beyond tokens](./14_Authorization_Beyond_Tokens.md)

## Further reading

- [RFC 7643 — SCIM Core Schema](https://www.rfc-editor.org/rfc/rfc7643)
- [RFC 7644 — SCIM Protocol](https://www.rfc-editor.org/rfc/rfc7644)
- [RFC 7642 — SCIM Definitions / Concepts](https://www.rfc-editor.org/rfc/rfc7642)
- [NIST SP 800-63-4](https://csrc.nist.gov/pubs/sp/800/63/4/final)
