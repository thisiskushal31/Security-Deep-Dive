# Applied patterns: tokens and request signing

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

How cryptography shows up in **product patterns**: JWTs, encrypted tokens, API/webhook HMAC signatures, and crypto agility — without turning this into an OAuth deep dive (that lives with [Identity](../Identity/README.md) / AppSec). Primitives: [3](./3_Asymmetric_Signatures_And_Certificates.md) · [10](./10_Hashing_MACs_And_Key_Derivation.md). Key custody: [5](./5_Key_Management_And_Modules.md).

## Concept

### Pattern map

| Pattern | Crypto job | Failure class |
|---------|------------|---------------|
| **Signed JWT (JWS)** | Integrity + authenticity of claims | `alg=none`, key confusion, forever keys |
| **Encrypted JWT (JWE)** / sealed blob | Confidentiality of claims | Encrypted but still trusted without authZ |
| **HMAC request signing** | Authenticate API/webhook payloads | Replay, canonicalization bugs |
| **Opaque server sessions** | Random id → server-side state | Predictable ids ([4](./4_Randomness_And_Key_Generation.md)) |
| **Dual keys / kid** | Rotation without outage | Missing `kid`; all tokens die on rotate |

```text
Client                         Server
  │  Authorization: Bearer <JWT> │
  │─────────────────────────────▶│ verify sig + exp + aud + iss
  │                              │ THEN authZ on claims / scopes
```

Signed ≠ authorized. Crypto proves the token was minted by a holder of the key; **your** policy decides what that means.

### JWT / JWS literacy (crypto slice)

| Header/claim | Role |
|--------------|------|
| `alg` | Signature/MAC algorithm — **allow-list** on verify |
| `kid` | Key id for rotation |
| `exp` / `nbf` | Time bound |
| `iss` / `aud` | Issuer / audience binding |
| `sub` | Subject — still need authZ |

| Do | Don’t |
|----|-------|
| Allow-list algorithms (`RS256`, `ES256`, `EdDSA`, `HS256` only if secret is strong and local) | Trust the token’s `alg` blindly |
| Use asymmetric verify for multi-service validation when possible | Share one HS256 secret with every microservice |
| Keep lifetimes short; refresh deliberately | Multi-day access tokens with sensitive scopes |
| Store secrets/PII server-side when possible | Put passwords or raw PANs in JWT payloads |

**`alg=none` and algorithm confusion** (for example treating an HMAC secret as an RSA key) are classic implementation bugs — use maintained JWT libraries and disable none.

### When you need JWE (encryption)

JWS protects against tampering; contents are still readable to anyone who obtains the token. Use **JWE** or server-side sessions when claims are confidential (or avoid putting them in the token). Encryption without signature/AEAD semantics still needs a clear integrity story — prefer library AEAD/JWE modes.

## Advanced

### HMAC API and webhook signing

```text
string_to_sign = canonical(method, path, timestamp, body_hash)
tag = HMAC(secret, string_to_sign)
```

| Control | Why |
|---------|-----|
| Canonicalization | Same bytes every time; forbid ambiguous JSON key orders if you sign JSON |
| Timestamp + skew window | Limits replay |
| Body hash (SHA-256) | Stable binding to payload |
| Versioned secrets | Dual-verify during rotation |
| Constant-time compare | Tag check |

Webhook endpoints must reject stale timestamps and unknown key ids. Log failures without logging the secret or full token.

### Opaque sessions vs JWT

| | Opaque session id | JWT access token |
|--|-------------------|------------------|
| Revocation | Delete server record | Need blocklist / short TTL / introspection |
| Size | Small cookie | Larger; watch headers |
| Crypto | CSPRNG id | Sign/verify path |
| Fits | Browser apps with session store | Service-to-service, mobile offline-ish claims |

Many systems use **both**: short JWT/API tokens plus revocable refresh/session records.

### Crypto agility

Design so algorithms and keys can change:

| Mechanism | Practice |
|-----------|----------|
| `kid` / key version on every artifact | Ciphertexts, tokens, signatures |
| Dual-verify window | Old + new keys accepted during rotate |
| Algorithm allow-lists in config | Not hard-coded forever in clients |
| Versioned `info` labels in HKDF | `v1/…` → `v2/…` ([10](./10_Hashing_MACs_And_Key_Derivation.md)) |

Agility without inventory is fiction — list every verifier in every service.

### Confusion hazards (checklist)

| Hazard | Symptom |
|--------|---------|
| HS256 secret = RSA private PEM mishandling | Forged tokens |
| Accepting tokens with wrong `aud` | Cross-app replay |
| Putting long-lived JWT in localStorage XSS path | Token theft (AppSec XSS) |
| Signing mutable claims without `exp` | Eternal authority |
| One webhook secret for all tenants | Cross-tenant forgery |

### Constant-time and error messages

Return the same client error for “bad signature,” “expired,” and “unknown kid” **when** detailed errors would help attackers probe key material — while logging distinct reasons server-side for operators.

## Apply

1. Inventory every JWT issuer/verifier; document `alg` allow-list and key location.  
2. Disable `none` and unexpected algs in tests that assert reject paths.  
3. Add `kid` + dual-verify to the next key rotation drill.  
4. For webhooks, implement timestamp windows and secret versioning.  
5. Decide session vs JWT per client type; document revocation.

## Self-check

- [ ] Explain why signed JWT is not an authZ decision  
- [ ] Name two JWT crypto foot-guns (`alg` / key confusion)  
- [ ] Sketch HMAC request signing with replay limits  
- [ ] Define crypto agility with `kid` + dual-verify  

## Next

- Back to [Cryptography README](./README.md) · TLS pitfalls [8](./8_TLS_And_PKI_Builder_Pitfalls.md) · PQC [9](./9_Algorithm_Transition_And_PQC.md)

## Further reading

- [OWASP — Key Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html)
- [OWASP — Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [OWASP Top 10:2025 — A04 Cryptographic Failures](https://owasp.org/Top10/2025/)
- [RFC 5869 — HKDF](https://www.rfc-editor.org/rfc/rfc5869.html)
- [AppSec — Access control and authentication](../AppSec/3_Access_Control_And_Authentication.md)
