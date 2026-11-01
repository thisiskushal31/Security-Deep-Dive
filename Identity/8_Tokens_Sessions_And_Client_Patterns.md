# Tokens, sessions, and client patterns

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

After federation or local login, the hard part is **holding power safely**: access tokens, refresh tokens, and RP (relying party) sessions. Most identity breaches after “we have SSO” are token theft, fat tokens in browsers, or revoke paths that do not work.

## Concept

### Three different credentials people conflate

| Credential | Proves / carries | Typical lifetime |
|------------|------------------|------------------|
| **ID token** | Authentication event to the client | Minutes |
| **Access token** | Authorization to call an RS (resource server) | Minutes |
| **Refresh token** | Ability to mint new access tokens | Hours–days (policy) |
| **RP session** | App-local “logged in” | App policy |

JWT (JSON Web Token) format (RFC 7519) is common for ID tokens and often for access tokens (RFC 9068 profile). Opaque access tokens remain valid — RS uses introspection (RFC 7662) instead of local JWT parse.

### Client shapes

| Shape | Token storage reality |
|-------|----------------------|
| **Confidential server** | Tokens in server-side session store; browser gets cookie |
| **SPA (single-page application)** public client | High XSS (cross-site scripting) risk if tokens sit in JS storage |
| **Native app** | OS-backed storage + system browser + PKCE (Proof Key for Code Exchange) |
| **BFF (Backend for Frontend)** | Browser talks only to BFF; BFF holds tokens |

RFC 10017 details browser-based app threats and patterns — prefer architectures that keep refresh tokens out of JavaScript.

## Advanced

### Sender constraints beat pure bearers

| Mechanism | Idea |
|-----------|------|
| **DPoP (Demonstrating Proof of Possession)** (RFC 9449) | Client proves key possession on each use; AS (authorization server) binds token to that key |
| **mTLS (mutual TLS) bound tokens** | Token usable only on mTLS session with bound cert |
| **Audience + resource indicators** (RFC 8707) | Token useless at the wrong API |

Stolen bearer tokens work until expiry; bound tokens raise the bar for replay from another host.

### JWT access token profile (RFC 9068) habits

| Habit | Why |
|-------|-----|
| AS signs with asymmetric keys; publish JWKS | RS validates without calling AS every time |
| Explicit `iss`, `exp`, `aud` | Interop + confused-deputy defense |
| Prefer minimal claims in access tokens | Privacy + token bloat |
| Do not put sensitive PII (personally identifiable information) in JWT bodies that browsers can read | Readable if leaked |

Crypto algorithm pitfalls: [Cryptography](../Cryptography/README.md).

### Revocation and introspection

| Tool | Use |
|------|-----|
| **Revocation** (RFC 7009) | Logout, compromise, offboarding — call it; do not only delete a cookie |
| **Introspection** (RFC 7662) | RS checks active status for opaque (or checked) tokens |
| **Short access TTL + refresh rotation** | Limits replay window; detect refresh reuse |

IdP / SaaS incident runbooks: [Defensive-Ops/10](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md).

### Session fixation and step-up

| Control | Intent |
|---------|--------|
| Rotate session ID at login | Kill fixation |
| Re-authenticate for sensitive actions | AAL (Authentication Assurance Level) step-up |
| Absolute + idle timeouts | Forever sessions break Zero Trust — [9](./9_Zero_Trust_Architecture.md) |

## Apply

1. Diagram where access and refresh tokens live for your main app (browser, BFF, mobile).  
2. If refresh tokens are in JS-accessible storage, plan a BFF or hardened pattern.  
3. Enable refresh rotation; alert on reuse.  
4. Wire logout to revocation endpoints; test with a live token after logout.  
5. For one high-value API, require audience match and evaluate DPoP or mTLS binding.

Lab IdPs / BFF-friendly stacks: [16](./16_Identity_Tooling_Map.md).

## Next

- [Zero Trust architecture](./9_Zero_Trust_Architecture.md)

## Further reading

- [RFC 7519 — JSON Web Token (JWT)](https://www.rfc-editor.org/rfc/rfc7519)
- [RFC 9068 — JWT Profile for OAuth 2.0 Access Tokens](https://www.rfc-editor.org/rfc/rfc9068)
- [RFC 9449 — OAuth 2.0 DPoP](https://www.rfc-editor.org/rfc/rfc9449)
- [RFC 7662 — Token Introspection](https://www.rfc-editor.org/rfc/rfc7662)
- [RFC 7009 — Token Revocation](https://www.rfc-editor.org/rfc/rfc7009)
- [RFC 8707 — Resource Indicators](https://www.rfc-editor.org/rfc/rfc8707)
- [RFC 10017 — OAuth 2.0 for Browser-Based Applications](https://www.rfc-editor.org/rfc/rfc10017)
- [RFC 9700 — OAuth 2.0 Security BCP](https://www.rfc-editor.org/rfc/rfc9700)
