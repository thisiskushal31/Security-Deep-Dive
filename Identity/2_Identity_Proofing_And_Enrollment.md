# Identity proofing and enrollment

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**Identity proofing** is how a CSP (credential service provider) gains confidence that an *applicant* is a real eligible person (when the service needs that). **Enrollment** binds that person to authenticators and a subscriber account. NIST SP 800-63A-4 is the normative home; this note is the program view.

## Concept

### Why proofing exists

Authentication alone answers “does this claimant control the authenticator?” Proofing answers “was that authenticator issued to the *right* human?” Without proofing, high AAL (Authentication Assurance Level) only protects a possibly fraudulent account.

| Outcome | Means |
|---------|--------|
| **IAL (Identity Assurance Level)** | Strength of proofing + enrollment for that subscriber |
| **Evidence** | Documents, records, digital credentials the applicant presents |
| **Validation / verification** | Checks that evidence is genuine and belongs to the applicant |
| **Enrollment code / binding** | Ties proofed identity to authenticators |

### Remote vs in-person (both are “proofing”)

| Channel | Program note |
|---------|--------------|
| Remote | Scales; needs anti-automation, presentation-attack resistance, carefully chosen evidence |
| Supervised / in-person | Higher confidence for some evidence types; cost and access trade-offs |
| Hybrid | Start remote, step up when risk or evidence fails |

SP 800-63A-4 is written so agencies (and adopters) can meet an IAL with multiple process designs — not one vendor ritual.

## Advanced

### Core process stages (timeless)

```text
Resolution ──▶ Evidence collection ──▶ Validation ──▶ Verification
                                                          │
                                                          ▼
                                              Enrollment / authenticator binding
                                                          │
                                                          ▼
                                              Ongoing account & record hygiene
```

| Stage | Failure mode if weak |
|-------|----------------------|
| Resolution | Duplicate or synthetic identities collide |
| Evidence | Forged or stolen documents accepted |
| Validation | Issuer/check path skipped or spoofable |
| Verification | Evidence not bound to the live applicant |
| Binding | Attacker enrolls MFA on a victim’s proofed account |

### Fraud and privacy are first-class

| Pressure | Design response |
|----------|-----------------|
| Automated enrollment attacks | Rate limits, bot resistance, step-up, monitoring (63A emphasizes anti-automation) |
| Stolen identity packages | Multi-evidence, issuer checks, anomaly signals — not “upload selfie once” theater |
| Over-collection of PII (personally identifiable information) | Minimize attributes; purpose limitation; retention limits |
| Exclusion / abandonment | Alternate evidence paths; usability testing with real populations |

### What *not* to confuse with proofing

| Activity | Home instead |
|----------|--------------|
| Day-to-day login strength | [Authenticators / AAL](./3_Authenticators_MFA_And_WebAuthn.md) |
| SSO (single sign-on) assertion quality | [Federation / FAL](./7_Federation_Assurance_And_RP_Validation.md) |
| Password hashing | [Cryptography](../Cryptography/README.md) |
| Cloud “identity verification” product demos | Vendor docs — map features back to IAL controls |

### CSP responsibilities that bite later

| Duty | Why IR (incident response) / GRC (Governance, Risk, and Compliance) care |
|------|-----------------------------------------------------------------------|
| Authenticator lifecycle | Lost/stolen device → recovery that does not undo proofing |
| Record accuracy | Wrong attributes federated to every RP (relying party) |
| Suspension / termination | Offboarding must kill federated sessions — [Defensive-Ops/10](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md) |

## Apply

1. For one high-impact service, write whether real-world identity is required (or pseudonymous is enough).  
2. If required, draft target IAL and list evidence types you *actually* can validate.  
3. Trace enrollment → first authenticator binding; mark where an attacker could insert themselves.  
4. Define recovery that requires comparable assurance (not “email me a reset and drop to IAL0”).  
5. List PII you store for proofing and a deletion/retention trigger.

## Next

- [Authenticators, MFA, and WebAuthn](./3_Authenticators_MFA_And_WebAuthn.md)

## Further reading

- [NIST SP 800-63A-4 — Identity Proofing and Enrollment](https://csrc.nist.gov/pubs/sp/800/63/A/4/final)
- [NIST SP 800-63-4 — Digital Identity Guidelines](https://csrc.nist.gov/pubs/sp/800/63/4/final)
