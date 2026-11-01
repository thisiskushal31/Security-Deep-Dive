# Federation assurance and RP validation

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**Federation** lets an IdP (identity provider) / CSP (credential service provider) assert authentication (and optionally attributes) to a separately administered **RP (relying party)**. NIST SP 800-63C-4 defines **FAL (Federation Assurance Level)** — how strongly that assertion is protected and presented. Protocols ([OIDC](./5_OpenID_Connect.md), [SAML](./6_SAML_Enterprise_SSO.md)) are carriers; FAL is the assurance bar.

## Concept

### Why FAL exists

IAL (Identity Assurance Level) and AAL (Authentication Assurance Level) can be perfect at the IdP and still fail the RP if an attacker **injects** or **replays** an assertion, or if the RP skips validation. FAL rates the *federation transaction*, not the IdP’s lobby signage.

| FAL grain | Program intent |
|-----------|----------------|
| **FAL1** | Signed assertion; audience-restricted; RP validates |
| **FAL2** | Stronger protection against assertion injection (often back-channel presentation) |
| **FAL3** | Holder-of-key / bound-authenticator presentation — high confidence presenter holds key material |

SP 800-63C examples map OIDC / SAML options to FALs **illustratively** — your deployment must meet the normative requirements end-to-end, not cherry-pick one row from a table.

### Trust before traffic

| Prerequisite | Meaning |
|--------------|---------|
| **Trust agreement** | Why the RP accepts this IdP (contract, federation authority, policy) |
| **Identifier & key establishment** | Entity IDs, JWKS (JSON Web Key Set) / certificates, endpoints |
| **Attribute policy** | Which claims the RP may use for authZ (authorization) |

Dynamic client registration and multi-IdP meshes increase operational risk — treat onboarding as security review, not a checkbox.

## Advanced

### RP validation is non-negotiable (all FALs)

| Validate | Against |
|----------|---------|
| Issuer | Expected IdP |
| Audience | This RP |
| Signature / MAC (message authentication code) | Keys established for that IdP |
| Freshness | `exp` / conditions / one-time use where required |
| Intended injection surface | Front-channel vs back-channel assumptions for your FAL |

```text
IdP ──assertion──▶ (front or back channel) ──▶ RP
                      │
                      ▼
              Validate then create RP session
              (do not “trust the redirect”)
```

### Injection and replay (the FAL2 story)

| Attack | Mitigation grain |
|--------|------------------|
| Attacker POSTs a stolen assertion to SP ACS (Assertion Consumer Service) | Back-channel retrieval; one-time artifacts; strict `InResponseTo` |
| Replay within validity window | `jti` / one-time assertion IDs; short lifetimes; server-side replay cache |
| Mix-up across clients | Per-client audience; OIDC mix-up mitigations |

### FAL3 and holder-of-key

Bearer assertions (anyone who steals them can present them) hit a ceiling. FAL3-class deployments require the presenter to prove control of a key bound to the assertion (SAML HoK profiles, bound authenticators, emerging OIDC patterns). Pair with [token binding / DPoP](./8_Tokens_Sessions_And_Client_Patterns.md) for API access tokens.

### Federation ≠ Zero Trust complete

Strong FAL still needs device posture, continuous authorization, and segmenting — [Zero Trust architecture](./9_Zero_Trust_Architecture.md). Federation is how identity *moves*; Zero Trust is how access *decides*.

## Apply

1. Pick one federated RP; write whether assertions arrive front-channel, back-channel, or both.  
2. Trace code/library validation of `iss` / `aud` / signature / time — list gaps.  
3. Target a FAL; note which protocol options you still need to enable.  
4. Confirm trust agreement + key rollover owner named.  
5. Test stolen-assertion replay in a **lab** IdP/RP pair; confirm detection or rejection.

## Next

- [Tokens, sessions, and client patterns](./8_Tokens_Sessions_And_Client_Patterns.md)

## Further reading

- [NIST SP 800-63C-4 — Federation and Assertions](https://csrc.nist.gov/pubs/sp/800/63/C/4/final)
- [NIST 800-63-4 FAL examples (HTML)](https://pages.nist.gov/800-63-4/sp800-63c/examples/)
- [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html)
- [OASIS SAML 2.0 Security Considerations](https://docs.oasis-open.org/security/saml/v2.0/saml-sec-consider-2.0-os.pdf)
