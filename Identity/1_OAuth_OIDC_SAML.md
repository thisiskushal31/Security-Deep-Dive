# OAuth, OIDC, SAML

[← Identity](./README.md)

Program view of **federation**: let a trusted IdP assert identity so apps do not store passwords. Protocol knobs and library how-to live in Tooling and System-Design.

## Roles (shared vocabulary)

| Role | Job |
|------|-----|
| **Resource owner** | User (or system) who owns the data |
| **Client** | App requesting access |
| **Authorization server / IdP** | Issues tokens or assertions |
| **Resource server** | API that checks tokens |

## When teams pick which

| Protocol family | Typical use |
|-----------------|-------------|
| **OAuth 2.x** | Delegated *authorization* (access to APIs) |
| **OIDC** | OAuth profile for *authentication* (ID token, userinfo) |
| **SAML 2.0** | Enterprise SSO (XML assertions); common in older IdPs |

Modern consumer/SaaS apps usually start with **OIDC**. Enterprise workforce SSO may still be SAML at the IdP edge with OIDC to apps.

## Program risks (not a full RFCs dump)

| Risk | Mitigation grain |
|------|------------------|
| Treating access token as proof of login | Use OIDC ID token / session for authn |
| Long-lived tokens in SPAs | BFF or confidential client patterns; short TTL |
| Confused deputy / wrong audience | Validate `aud`, `iss`, signature |
| Implicit flow / credential leaks | Prefer auth code + PKCE |

## Depth elsewhere

- Stack how-to → [Tooling Specs-Standards/OAuth](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Specs-Standards/OAuth)
- Architecture patterns → [System-Design Federated Identity](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security/2_Federated_Identity.md)
- CI cloud login → [Handbook OIDC CI](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/5_OIDC_CI_And_Least_Privilege.md)

## Next

- [Zero Trust](./2_Zero_Trust.md)
