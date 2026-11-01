# Zero Trust maturity and the identity pillar

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

Architecture ideas ([SP 800-207](./9_Zero_Trust_Architecture.md)) need a **maturity path**. CISA’s **Zero Trust Maturity Model (ZTMM) v2** gives pillars and stages. OMB **M-22-09** states concrete federal expectations (especially phishing-resistant MFA) that many private programs echo.

## Concept

### ZTMM pillars

| Pillar | Identity-relevant grain |
|--------|-------------------------|
| **Identity** | Users/entities proven, authenticated, authorized dynamically |
| **Devices** | Inventory, health, compliance as policy input |
| **Networks** | Segment, encrypt, least privilege paths |
| **Applications & workloads** | AuthN/Z to apps; secure SDLC (software development lifecycle) |
| **Data** | Inventory, protect, access governed |

Cross-cutting: **visibility & analytics**, **automation & orchestration**, **governance**.

### Maturity stages (gradient, not a cliff)

| Stage | Identity pillar feel |
|-------|----------------------|
| **Traditional** | Passwords, limited MFA, static directories, broad VPN trust |
| **Initial** | MFA expanding; some federation; early risk signals |
| **Advanced** | Phishing-resistant options; richer attributes; adaptive access |
| **Optimal** | Continuous validation; strong identity for users *and* machines; automated response |

Treat stages as a map for investment — not a marketing badge.

## Advanced

### M-22-09 pressures that travel beyond government

| Expectation | Program translation |
|-------------|---------------------|
| Enterprise-managed identities for staff | Central IdP (identity provider); reduce local snowflake accounts |
| **Phishing-resistant MFA** | WebAuthn / PIV-class authenticators — [3](./3_Authenticators_MFA_And_WebAuthn.md) |
| Device visibility | MDM/inventory feeding access policy |
| Encrypt traffic; prefer hardened patterns | Align with Networks / Cloud-Security |
| Attribute-based / least-privilege access to apps | Kill shared admin; just-in-time elevation |

Password theater (rotation cosplay without phishing resistance) conflicts with modern 800-63B guidance and M-22-09’s direction — prefer length/strength + breach checks + phishing-resistant MFA over ritual complexity.

### Identity pillar checklist (builder view)

| Capability | Weak signal | Stronger signal |
|------------|-------------|-----------------|
| Authentication | SMS MFA everywhere | Passkeys / hardware; AAL targets set |
| Federation | Unsigned SAML; no `aud` checks | FAL-aware RP validation — [7](./7_Federation_Assurance_And_RP_Validation.md) |
| Authorization | Coarse AD groups forever | Just-enough, reviewed, time-bounded roles |
| Privileged access | Standing domain admin | JIT (just-in-time) + strong auth + session recording policy |
| Non-human identity | Shared service passwords | Workload identity — [11](./11_Workload_And_Non_Human_Identity.md) |
| Visibility | Auth logs unparsed | IdP + endpoint + PEP (policy enforcement point) joined in SIEM — [Defensive-Ops](../Defensive-Ops/README.md) |

### Governance hooks

| Hook | Why |
|------|-----|
| Identity standards in architecture review | Stops shadow OAuth apps |
| Joiner–mover–leaver tied to IdP | Orphan accounts break ZT |
| Exception register with expiry | “Temporary” broad access becomes permanent |

GRC nesting of CSF (Cybersecurity Framework) outcomes: [GRC](../GRC/README.md).

## Apply

1. Score your identity pillar honestly (traditional → optimal) with three bullets of evidence.  
2. List apps still on password-only or SMS MFA.  
3. Pick one phishing-resistant milestone for workforce this half.  
4. Name owners for joiner–mover–leaver vs IdP automation gaps.  
5. Align one ZT metric (e.g., % staff on phishing-resistant MFA) with [Defensive-Ops metrics](../Defensive-Ops/README.md).

## Next

- [Workload and non-human identity](./11_Workload_And_Non_Human_Identity.md)

## Further reading

- [CISA — Zero Trust Maturity Model](https://www.cisa.gov/resources-tools/resources/zero-trust-maturity-model)
- [OMB M-22-09 — Federal Zero Trust Strategy](https://www.whitehouse.gov/wp-content/uploads/2022/01/M-22-09.pdf)
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [NIST SP 800-63B-4](https://csrc.nist.gov/pubs/sp/800/63/B/4/final)
