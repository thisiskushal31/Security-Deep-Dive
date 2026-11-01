# Digital identity and assurance levels

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

A **digital identity** is unique in the context of an online service. It may or may not map to a known real-world person. NIST SP 800-63-4 treats identity work as three functions you size independently: **proof** who someone is, **authenticate** that the same subscriber returned, and **federate** that result to another party.

## Concept

### Three assurance dials (xAL)

| Dial | Question | Volume |
|------|----------|--------|
| **IAL (Identity Assurance Level)** | How strongly was this person proofed at enrollment? | [SP 800-63A](./2_Identity_Proofing_And_Enrollment.md) |
| **AAL (Authentication Assurance Level)** | How strongly do we authenticate them each session? | [SP 800-63B](./3_Authenticators_MFA_And_WebAuthn.md) |
| **FAL (Federation Assurance Level)** | How strongly is the assertion delivered to the RP (relying party)? | [SP 800-63C](./7_Federation_Assurance_And_RP_Validation.md) |

You can run high AAL with low IAL (strong login for a pseudonymous account) or high IAL with weaker day-to-day auth — the combination is a **risk choice**, not a compliance checkbox.

### Roles you will see everywhere

| Role | Job |
|------|-----|
| **Applicant / claimant / subscriber** | Person progressing through proof → use |
| **CSP (credential service provider)** | Proofs, enrolls, issues/manages authenticators |
| **IdP (identity provider)** | Issues federated assertions (often the CSP or adjacent) |
| **RP / verifier** | Trusts an assertion or verifies an authenticator to open a session |
| **Authenticator** | Something the subscriber controls (password, OTP (one-time password), passkey, …) |

### When federation enters

If the RP does not run authentication itself and instead consumes an assertion from an IdP, **FAL** applies on top of whatever IAL/AAL the IdP claims. Federation depth: [7](./7_Federation_Assurance_And_RP_Validation.md). Protocol families: [OAuth](./4_OAuth_Authorization.md), [OIDC](./5_OpenID_Connect.md), [SAML](./6_SAML_Enterprise_SSO.md).

## Advanced

### Risk-based selection (not “pick 2 everywhere”)

SP 800-63-4 expects organizations to assess impact of:

| Error class | Example impact |
|-------------|----------------|
| Wrong person enrolled | Fraudulent benefits, account takeover at birth |
| Wrong person authenticated | Session abuse, data exfil |
| Forged / replayed federation | RP session for attacker without IdP login |

Then select xAL baselines and **tailor** (compensate, partition sensitive functions to higher assurance, keep low-risk features reachable at lower assurance).

Also weigh: security/fraud, **privacy** (personal data in proofing), and **customer experience** (abandonment, inclusion). Theater is declaring AAL3 on paper while shipping SMS OTP (short message service one-time password) everywhere.

### What each level roughly buys you (program view)

| Level | IAL grain | AAL grain | FAL grain |
|-------|-----------|-----------|-----------|
| **1** | Self-asserted / limited proofing paths (rev. 4 reshaped IAL1) | Single-factor or basic MFA | Signed assertion, audience-restricted |
| **2** | Evidence + verification appropriate to moderate risk | MFA with replay/phishing hardening per 63B | Stronger injection / replay protection (often back-channel) |
| **3** | High confidence in real-world identity | Phishing-resistant, verifier-impersonation resistant authenticators | Holder-of-key / bound authenticator presentation |

Exact normative tables live in the suite — treat this as orientation, then read the volume for the xAL you claim.

### Partitioning services

| Pattern | Why |
|---------|-----|
| Public browse at low xAL; change bank details at high | Reduce friction without lowering high-impact bars |
| Workforce SSO at AAL2; privileged break-glass at AAL3 | Match authenticator cost to blast radius |
| Pseudonymous community accounts (no IAL) + strong AAL | Privacy-preserving where real-world identity is not required |

## Apply

1. List three services (customer-facing, workforce, machine/API).  
2. For each, write the worst credible identity error and who gets hurt.  
3. Draft target IAL/AAL/FAL (or “IAL n/a”) — one line each.  
4. Mark where today’s MFA or SSO **fails** that target (SMS, forever sessions, unsigned SAML).  
5. Pick one function to partition to a higher xAL this quarter.

## Next

- [Identity proofing and enrollment](./2_Identity_Proofing_And_Enrollment.md)

## Further reading

- [NIST SP 800-63-4 — Digital Identity Guidelines](https://csrc.nist.gov/pubs/sp/800/63/4/final)
- [NIST SP 800-63A-4](https://csrc.nist.gov/pubs/sp/800/63/A/4/final)
- [NIST SP 800-63B-4](https://csrc.nist.gov/pubs/sp/800/63/B/4/final)
- [NIST SP 800-63C-4](https://csrc.nist.gov/pubs/sp/800/63/C/4/final)
