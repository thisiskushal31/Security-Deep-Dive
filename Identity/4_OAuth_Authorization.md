# OAuth authorization

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**OAuth 2.x** is an **authorization** framework: a client obtains limited access to a protected resource, usually on behalf of a resource owner. It is **not** by itself an authentication protocol — that is [OpenID Connect](./5_OpenID_Connect.md). Current security practice is framed by **RFC 9700** (OAuth 2.0 Security BCP); **OAuth 2.1** (Internet-Draft) consolidates that advice into a single revised framework.

## Concept

### Roles

| Role | Job |
|------|-----|
| **Resource owner** | User (or system) who owns the data |
| **Client** | Application requesting access |
| **Authorization server** | Authenticates the owner (as needed) and issues tokens |
| **Resource server** | API that accepts access tokens |

```text
Resource owner ──consent──▶ Authorization server ──access token──▶ Client
                                                                      │
                                                                      ▼
                                                              Resource server
```

### Authorization vs authentication

| Goal | Use |
|------|-----|
| Call an API with scoped access | OAuth access token |
| Prove *who logged in* to your app | OIDC ID token / session — [5](./5_OpenID_Connect.md) |
| Enterprise browser SSO with XML assertions | Often [SAML](./6_SAML_Enterprise_SSO.md) at the IdP edge |

Treating an access token as “logged-in user proof” without OIDC (or equivalent) is a classic program bug.

### Grants that still make sense (RFC 9700 lens)

| Grant / pattern | Use when |
|-----------------|----------|
| **Authorization code** + **PKCE (Proof Key for Code Exchange)** | Browser and native apps; default modern interactive path |
| **Client credentials** | Confidential client acting as itself (no user) |
| **Refresh token** (carefully) | Long-lived access without re-prompt — bind, rotate, revoke |

| Avoid / legacy | Why |
|----------------|-----|
| Implicit flow | Tokens in front channel; deprecated by security BCP |
| Resource owner password credentials | Trains users to type passwords into clients |
| Unauthenticated `http://` redirect URIs | Code/token interception |

## Advanced

### Public vs confidential clients

| Client | Expectation |
|--------|-------------|
| **Confidential** | Can hold a client secret / key (server-side) |
| **Public** | Cannot keep a secret (SPA (single-page application), native) — **PKCE required** (RFC 7636 / 8252 / 9700) |

Native apps: external user-agent (system browser) + PKCE (RFC 8252). Browser-based apps: prefer BFF (Backend for Frontend) or other patterns in RFC 10017 — details in [8](./8_Tokens_Sessions_And_Client_Patterns.md).

### Audience, scope, and confused deputy

| Control | Intent |
|---------|--------|
| **Scope** | Coarse authorization downscope at issue time |
| **Audience / resource** (RFC 8707 resource indicators) | Token usable only at intended RS (resource server) |
| Exact **redirect URI** match | Blocks code leakage to attacker-controlled endpoints |
| **state** / OIDC **nonce** | CSRF (cross-site request forgery) / mix-up mitigations (nonce is OIDC) |

### OAuth 2.1 (draft) as a reading aid

OAuth 2.1 aims to obsolete RFC 6749/6750 as the “one doc” story by **removing insecure features** and baking in PKCE and BCP requirements. Until it is an RFC, implement to **RFC 9700 + 6749** and track the draft for consolidation — do not wait for the number to start killing implicit flow.

Stack install / library how-to: [Tooling OAuth](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Specs-Standards/OAuth).

## Apply

1. Inventory every OAuth client: public vs confidential; grant types enabled.  
2. Disable implicit and password grants where still on.  
3. Require PKCE for all public clients; prefer it for confidential too.  
4. For each API, define expected `aud` / resource and reject tokens that do not match.  
5. Confirm refresh tokens are rotatable and revocable on logout / compromise.

## Next

- [OpenID Connect](./5_OpenID_Connect.md)

## Further reading

- [RFC 6749 — OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/rfc/rfc6749)
- [RFC 9700 — Best Current Practice for OAuth 2.0 Security](https://www.rfc-editor.org/rfc/rfc9700)
- [RFC 7636 — PKCE](https://www.rfc-editor.org/rfc/rfc7636)
- [RFC 8252 — OAuth 2.0 for Native Apps](https://www.rfc-editor.org/rfc/rfc8252)
- [RFC 10017 — OAuth 2.0 for Browser-Based Applications](https://www.rfc-editor.org/rfc/rfc10017)
- [draft-ietf-oauth-v2-1 — OAuth 2.1](https://datatracker.ietf.org/doc/draft-ietf-oauth-v2-1/)
