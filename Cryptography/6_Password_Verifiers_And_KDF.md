# Password verifiers and key derivation

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

How to store passwords and similar secrets-at-rest so a database leak does not immediately become a credential dump — and how password-based KDFs relate. This is **not** reversible encryption of passwords. Goals: [1](./1_Goals_Threat_Model_And_Misuse.md). Key custody for peppers: [5](./5_Key_Management_And_Modules.md).

## Concept

### Hashing passwords ≠ encrypting passwords

| Approach | Recover password? | Use for login? |
|----------|-------------------|----------------|
| Store plaintext | Yes | Catastrophic |
| Encrypt password (AES) | Yes with key | Attacker who gets key + DB wins; often unnecessary |
| Fast hash (SHA-256 alone) | Offline cracking is cheap | Insufficient |
| Slow, salted password hash (Argon2id / bcrypt / scrypt / PBKDF2) | Not by design | Correct pattern for verifiers |

Login check: hash the candidate with the same parameters and compare in constant time to the stored verifier. You never need the plaintext password after registration/reset.

### Salt

A **salt** is a per-password random value stored alongside the hash.

| Without salt | With unique salt |
|--------------|------------------|
| Identical passwords → identical hashes | Identical passwords → different hashes |
| Rainbow tables / batch cracks scale | Attacker must attack each row |

Generate salts with a CSPRNG ([4](./4_Randomness_And_Key_Generation.md)); do not reuse one global salt for all users.

### Work factor

Password hashing must be **intentionally slow** (and for Argon2id/scrypt, memory-hard) so offline guessing is expensive. Tune work factors to current hardware budgets; plan to raise them over time.

## Advanced

### Algorithm preference (OWASP Password Storage)

Prefer maintained library defaults that implement:

| Preference order (typical) | Notes |
|----------------------------|-------|
| **Argon2id** | Modern default recommendation when available |
| **scrypt** | Memory-hard alternative |
| **bcrypt** | Widely deployed; mind input length limits (~72 bytes for classic bcrypt) |
| **PBKDF2** | Acceptable when required (for example FIPS-aligned stacks); use adequate iterations and HMAC-SHA-256 class PRF |

Exact parameters change as hardware improves — follow the current OWASP Password Storage Cheat Sheet and your compliance profile rather than copying blog numbers forever.

### Pepper (optional defense in depth)

A **pepper** is a secret added to password hashing that is **not** stored in the database (vault/HSM). Peppers can help when the DB leaks but the vault does not. They complicate rotation: changing a pepper requires re-hashing or dual-verify logic. Treat peppers as secrets under [5](./5_Key_Management_And_Modules.md) / [7](./7_Cryptographic_Storage_And_Secrets.md).

### NIST SP 800-63B (verifiers) — literacy

NIST SP 800-63B (Digital Identity Guidelines — authentication and lifecycle management) discusses authenticator and verifier requirements for federal-aligned identity systems. For builders: store verifiers using approved techniques; do not impose arbitrary composition rules that push users into predictable patterns; prefer longer randomly generated secrets or modern authenticators where the product allows. Read the current 63B revision you must comply with; do not invent policy from memory.

### PBKDF2 and SP 800-132

NIST SP 800-132 discusses password-based key derivation for **storage applications** (deriving keys from passwords). That is adjacent but not identical to “password verifier for login.”

| Goal | Mechanism family |
|------|------------------|
| Verify login password | Password hashing API (Argon2id/bcrypt/…) |
| Derive an encryption key from a passphrase | PBKDF / Argon2 as KDF with salt; then use AEAD ([2](./2_Symmetric_AEAD_And_Modes.md)) |
| Derive keys from **high-entropy** secrets (protocol IKM) | **HKDF** / SP 800-108 — not Argon2 — [10](./10_Hashing_MACs_And_Key_Derivation.md) |

Do not use a single fast hash iteration as either a verifier or a KDF. Do not use HKDF as your password hasher.

When PBKDF2 is required (FIPS-aligned stacks), OWASP’s Password Storage guidance currently points at high iteration counts with HMAC-SHA-256 (on the order of hundreds of thousands of iterations — verify the **current** cheat sheet before freezing numbers in policy).

### Upgrading legacy hashes

When migrating from MD5/SHA-1/unsalted SHA-256:

```text
on login success with legacy verifier:
  rehash with Argon2id (or chosen modern default)
  store new verifier + params
  invalidate legacy encoding
```

Optionally wrap legacy hashes (“hash the old hash”) as an intermediate — prefer rehash-on-login with a deadline to force resets for dormant accounts.

### What not to build

| Anti-pattern | Why |
|--------------|-----|
| Client-only hashing “so server never sees password” without a real auth protocol | Usually broken or redundant with TLS; easy to get wrong |
| Decryptable password “for support” | Support needs reset flows, not password reveal |
| Shared salt across tenants | Cross-user acceleration |
| Truncating bcrypt inputs silently | Unexpected collisions / weakened secrets |

### Stored verifier format (practical)

Persist enough metadata to verify and to upgrade later:

```text
$argon2id$v=19$m=...,t=...,p=...$<salt>$<digest>
```

Or a structured row: `{ algo, params, salt, hash, pepper_id? }`. Never store “hash only” without algorithm identity — upgrades become archaeology.

### Online vs offline threat

| Threat | Primary controls |
|--------|------------------|
| Online guessing at login | Rate limits, lockouts/backoff, MFA, anomaly detection |
| Offline cracking after DB theft | Salt + slow/memory-hard hash + pepper + minimize breach |

Password hashing buys time against offline attacks; it does not replace online abuse controls ([AppSec](../AppSec/README.md) / Identity).

### Composition rules vs length

Modern guidance generally favors **longer passwords / passphrases** and breach checks over complex composition theater that produces `Winter2024!`. Prefer:

- block known-breached passwords,  
- allow paste / password managers,  
- encourage MFA,  
- follow the 63B revision you must meet for verifier and authenticator rules.

## Apply

1. Confirm the login path uses a maintained password-hashing API with unique salts.  
2. Document algorithm + parameters + pepper location (if any) in a security design note.  
3. Schedule work-factor increases; add migration for dormant users.  
4. Align SSO/passwordless roadmaps so fewer passwords are stored at all.  
5. Test constant-time compare (or library verify) to avoid timing leaks on verifiers.

## Self-check

- [ ] Explain why AES of passwords is the wrong default  
- [ ] Define salt vs pepper  
- [ ] Order Argon2id / scrypt / bcrypt / PBKDF2 as product defaults  
- [ ] Sketch rehash-on-login migration  

## Next

- [Cryptographic storage and secrets](./7_Cryptographic_Storage_And_Secrets.md)

## Further reading

- [OWASP — Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [NIST SP 800-63B-4 — Authentication and Authenticator Lifecycle](https://csrc.nist.gov/pubs/sp/800/63/b/4/final)
- [NIST SP 800-132 — Password-Based Key Derivation](https://csrc.nist.gov/pubs/sp/800/132/final)
- [OWASP Proactive Controls — C2 Cryptography](https://top10proactive.owasp.org/the-top-10/c2-crypto/)
