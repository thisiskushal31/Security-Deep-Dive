# OpenID Connect

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**OpenID Connect (OIDC)** is an identity layer on top of OAuth 2.0. It lets a client verify the end-user’s identity based on authentication performed by an OpenID Provider (OP) and obtain basic profile claims via interoperable JWTs (JSON Web Tokens).

## Concept

### OAuth alone vs OIDC

| Need | Mechanism |
|------|-----------|
| Access an API | OAuth **access token** |
| Know *who* authenticated | OIDC **ID token** (and/or UserInfo) |
| Both | Authorization Code flow returning ID token + access token |

```text
RP (client) ──AuthN request──▶ OP (authorization server)
                 │
                 ▼
         ID token (who) + access token (what APIs)
```

### Core artifacts

| Artifact | Role |
|----------|------|
| **ID token** | JWT asserting authentication event + subject (`sub`) + audience (`aud` = client ID) |
| **UserInfo endpoint** | Optional claims about the end-user, authorized by access token |
| **Discovery** (`/.well-known/openid-configuration`) | OP metadata: endpoints, JWKS (JSON Web Key Set) URI, supported algs |
| **JWKS** | Public keys to verify signatures |

### Flows you still want

| Flow | Program note |
|------|--------------|
| **Authorization Code** (+ PKCE (Proof Key for Code Exchange) for public clients) | Default; ID token via front channel and/or token endpoint |
| **Hybrid** | Code + front-channel ID token — more moving parts; validate carefully |
| **Implicit** (OIDC) | Legacy; avoid for new work (align with [RFC 9700](./4_OAuth_Authorization.md)) |

## Advanced

### Claims the RP must treat as security controls

| Claim | Check |
|-------|-------|
| `iss` | Exact expected issuer |
| `aud` | Includes this client ID |
| `exp` / `iat` / `nbf` | Time window sane; clock skew bounded |
| `nonce` | Matches the value the client minted (replay / mix-up aid) |
| `at_hash` / `c_hash` (when present) | Bind ID token to access token / code where spec requires |
| Signature | Via OP JWKS; reject `alg=none` and unexpected algs |

Never accept an ID token meant for another client. Never use an **access token** as a substitute ID token.

### Session at the RP

OIDC authenticates once at the OP; the RP still needs its **own** session (cookie or equivalent) with:

| Property | Why |
|----------|-----|
| Logout / revoke path | OP logout ≠ every RP session dead unless you design it |
| Step-up | Re-auth when risk rises (AAL (Authentication Assurance Level) bump) |
| Binding to client type | SPA (single-page application) vs BFF (Backend for Frontend) — [8](./8_Tokens_Sessions_And_Client_Patterns.md) |

### Mapping to federation assurance

OIDC ID tokens are the assertion type SP 800-63C uses in FAL (Federation Assurance Level) examples. Front-channel vs back-channel delivery and holder-of-key options change achievable FAL — [7](./7_Federation_Assurance_And_RP_Validation.md).

### Workforce vs customer

| Pattern | Typical stack |
|---------|---------------|
| Customer “Login with …” | OIDC to social/enterprise OPs |
| Workforce SaaS | OIDC *or* [SAML](./6_SAML_Enterprise_SSO.md) from corporate IdP (Identity Provider); many IdPs speak both |

CI pipelines using OIDC to cloud roles: [DevOps-Handbook](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/5_OIDC_CI_And_Least_Privilege.md) — different audience (workloads), same claim-validation discipline.

## Apply

1. Point a lab RP at an OP discovery document; cache JWKS with rotation in mind.  
2. Log (redacted) ID token validation failures by reason (`aud`, `iss`, `nonce`, sig).  
3. Confirm access tokens are not parsed as proof of login in app code.  
4. Document RP session lifetime vs OP session vs refresh token lifetime.  
5. Test logout: OP logout, RP logout, and token revocation separately.

## Next

- [SAML enterprise SSO](./6_SAML_Enterprise_SSO.md)

## Further reading

- [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html)
- [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html)
- [RFC 9700 — OAuth 2.0 Security BCP](https://www.rfc-editor.org/rfc/rfc9700)
- [NIST SP 800-63C-4 — Federation and Assertions](https://csrc.nist.gov/pubs/sp/800/63/C/4/final)
