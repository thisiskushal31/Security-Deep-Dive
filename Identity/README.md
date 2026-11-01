# Identity

[← README](../README.md)

Digital identity as a **program**: prove who someone is (when you must), authenticate them strongly, federate assertions to apps, authorize finely, run joiner–mover–leaver, and operate access as if the network cannot be trusted. Named IdP/PAM/IGA **software** installs live in Tooling; HLD trade-offs live in System-Design; cloud IAM product knobs live in Cloud-Security.

```text
Proof (IAL) ──▶ Authenticate (AAL) ──▶ Federate (FAL) ──▶ Authorize / session
         │                                    │
         ▼                                    ▼
   Lifecycle / SCIM                    Zero Trust + PAM / IGA
         │                                    │
         └────────── detect (ITDR) ◀──────────┘
```

IAL (Identity Assurance Level), AAL (Authentication Assurance Level), and FAL (Federation Assurance Level) are the NIST SP 800-63-4 decision language used throughout this folder.

## Learning path

| # | Topic |
|---|--------|
| 1 | [Digital identity and assurance levels](./1_Digital_Identity_And_Assurance_Levels.md) |
| 2 | [Identity proofing and enrollment](./2_Identity_Proofing_And_Enrollment.md) |
| 3 | [Authenticators, MFA, and WebAuthn](./3_Authenticators_MFA_And_WebAuthn.md) |
| 4 | [OAuth authorization](./4_OAuth_Authorization.md) |
| 5 | [OpenID Connect](./5_OpenID_Connect.md) |
| 6 | [SAML enterprise SSO](./6_SAML_Enterprise_SSO.md) |
| 7 | [Federation assurance and RP validation](./7_Federation_Assurance_And_RP_Validation.md) |
| 8 | [Tokens, sessions, and client patterns](./8_Tokens_Sessions_And_Client_Patterns.md) |
| 9 | [Zero Trust architecture](./9_Zero_Trust_Architecture.md) |
| 10 | [Zero Trust maturity and the identity pillar](./10_Zero_Trust_Maturity_And_Identity_Pillar.md) |
| 11 | [Workload and non-human identity](./11_Workload_And_Non_Human_Identity.md) |
| 12 | [Identity threats, detect, and harden](./12_Identity_Threats_Detect_And_Harden.md) |
| 13 | [Lifecycle, SCIM, and directories](./13_Lifecycle_SCIM_And_Directories.md) |
| 14 | [Authorization beyond tokens](./14_Authorization_Beyond_Tokens.md) |
| 15 | [Privileged access and IGA](./15_Privileged_Access_And_IGA.md) |
| 16 | [Identity tooling map](./16_Identity_Tooling_Map.md) |

**Suggested order:** 1 → 12, then 13 → 15 (lifecycle / authZ / PAM), finish at **16** for lab software picks. Assurance language first; protocols after authenticators; Zero Trust after federation; threats before tooling shopping.

## What maps where

| Concern | Note |
|---------|------|
| IAL / AAL / FAL selection | 1–3, 7 |
| OAuth vs OIDC vs SAML | 4–6 |
| Token binding, BFF (Backend for Frontend), revoke | 8 |
| ZTA (Zero Trust Architecture) tenets and maturity | 9–10 |
| Service / SPIFFE (Secure Production Identity Framework for Everyone) identity | 11 |
| Token theft, IdP compromise, ITDR (identity threat detection and response) | 12 |
| JML / SCIM / directories | 13 |
| RBAC / ABAC / ReBAC / policy engines | 14 |
| PAM / IGA / SoD | 15 |
| OSS + commercial tool pick-list | 16 |

## Depth elsewhere (do not duplicate)

| Topic | Home |
|-------|------|
| IdP / PAM / IGA / SPIRE **install** | [Tooling Security/Identity](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity) |
| OAuth stack / library how-to | [Tooling Specs-Standards/OAuth](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Specs-Standards/OAuth) |
| Federated identity HLD / Auth vs ZT trade-offs | [System-Design](https://github.com/thisiskushal31/System-Design-Concepts) |
| Cloud IAM / tenant federation knobs | [Cloud-Security](../Cloud-Security/README.md) |
| CI OIDC (OpenID Connect) login | [DevOps-Handbook OIDC CI](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/5_OIDC_CI_And_Least_Privilege.md) |
| Password storage / JWT (JSON Web Token) crypto pitfalls | [Cryptography](../Cryptography/README.md) |
| IdP / SaaS IR (incident response) | [Defensive-Ops/10](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md) |
| Broken access / session flaws | [AppSec](../AppSec/README.md) |
| Wire / mTLS (mutual Transport Layer Security) / mesh | Networks · Containerization |

## Further reading (suite anchors)

- [NIST SP 800-63-4 — Digital Identity Guidelines](https://csrc.nist.gov/pubs/sp/800/63/4/final)
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [RFC 9700 — OAuth 2.0 Security BCP](https://www.rfc-editor.org/rfc/rfc9700)
- [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html)
- [CISA Zero Trust Maturity Model](https://www.cisa.gov/resources-tools/resources/zero-trust-maturity-model)
