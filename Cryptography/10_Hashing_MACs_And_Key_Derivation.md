# Hashing, MACs, and key derivation

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

Integrity primitives builders actually call: **cryptographic hashes**, **HMAC (Hash-based Message Authentication Code)**, and **KDFs (Key Derivation Functions)** such as **HKDF (HMAC-based Key Derivation Function)** — and how they differ from password hashing. AEAD often wraps integrity for you ([2](./2_Symmetric_AEAD_And_Modes.md)). Password verifiers: [6](./6_Password_Verifiers_And_KDF.md). Signatures when you need public verification: [3](./3_Asymmetric_Signatures_And_Certificates.md).

## Concept

### Three jobs people confuse

| Job | Right tool family | Wrong tool |
|-----|-------------------|------------|
| Fingerprint a file / detect accidental change | Cryptographic hash (SHA-256, SHA-3, …) | Encryption |
| Prove a message came from someone who knows a **shared secret** | HMAC / keyed MAC | Bare hash of the message |
| Turn key material into many purpose-bound keys | HKDF / SP 800-108 KDF | `SHA256(secret + "encrypt")` homemade |
| Store a login password | Argon2id / bcrypt / scrypt / PBKDF2 | Fast hash or HKDF alone |

```text
hash(data)           → digest          (no key; anyone can recompute)
HMAC(key, data)      → tag             (forgery needs the key)
HKDF(ikm, salt, info)→ keys…         (expand high-entropy secrets)
PBKDF/Argon2(pw,salt)→ verifier/key  (stretch low-entropy secrets)
```

### Cryptographic hash properties (builder view)

A hash maps arbitrary input to a fixed-length digest. For security use you care about:

| Property | Meaning |
|----------|---------|
| **Preimage resistance** | Hard to find input for a given digest |
| **Second-preimage resistance** | Hard to find a different input with the same digest as a given input |
| **Collision resistance** | Hard to find any two inputs with the same digest |

Broken or weak hashes (MD5, SHA-1 for many security uses) fail collision or practical attack stories — do not use them for signatures, certs, or new integrity designs ([9](./9_Algorithm_Transition_And_PQC.md)).

Hashes alone do **not** authenticate: an attacker who can change `data` can also recompute `hash(data)` unless a key or signature is involved.

### HMAC

HMAC combines a secret key with a hash function (FIPS 198-1 family) to produce a tag. Typical product uses:

| Use | Notes |
|-----|-------|
| API request signing | Tag over method + path + body + timestamp |
| Webhook verification | Provider sends HMAC of payload |
| Integrity of ciphertext when not using AEAD | Prefer Encrypt-then-MAC with separate keys ([2](./2_Symmetric_AEAD_And_Modes.md)) |
| Building block inside HKDF / TLS | Prefer library HKDF over hand-rolled HMAC chains |

Compare tags with a **constant-time** compare (language `hmac.compare_digest` / equivalent). Early-exit `==` on bytes can leak timing.

### HKDF vs password-based KDFs

| | **HKDF** (RFC 5869; also SP 800-108 family context) | **Password KDF** (Argon2id / scrypt / PBKDF2 / SP 800-132) |
|--|------------------------------------------------------|--------------------------------------------------------------|
| Input | High-entropy key material (shared secret, KEM output) | Low-entropy password / PIN |
| Goal | Extract/expand into labeled keys | Slow guessing; produce verifier or key |
| Memory-hard? | No (not the point) | Yes for modern password hashes |
| Use in TLS 1.3 | Key schedule | Not for login password storage |

```text
HKDF-Extract(salt, IKM) → PRK
HKDF-Expand(PRK, info, L) → OKM  (info = purpose label / context)
```

**Never** replace Argon2id with HKDF for password storage. **Never** replace HKDF with a single unsalted SHA-256 for protocol key schedules when a maintained HKDF is available.

## Advanced

### Choosing a hash for new work

| Situation | Prefer |
|-----------|--------|
| General integrity / HMAC | SHA-256 or SHA-384/512 per policy |
| Federal-aligned / transition | Follow current SP 800-131A / agency profile ([9](./9_Algorithm_Transition_And_PQC.md)) |
| Password storage | Not a general hash — [6](./6_Password_Verifiers_And_KDF.md) |
| File dedup / cache keys | Hash OK; do not treat as authentication |

### Truncation and length-extension

| Topic | Builder habit |
|-------|----------------|
| Truncating digests | Only when a standard requires it; understand collision impact |
| Length-extension on Merkle–Damgård hashes | Do not use bare `H(secret \|\| message)` as a MAC — use HMAC |
| “Checksum” CRCs | Not cryptographic integrity |

### SP 800-108 KDFs

NIST SP 800-108 specifies KDFs in **Counter / Feedback / Double-Pipeline** modes using PRFs such as HMAC. Libraries often expose HKDF or “KDF in counter mode” helpers. Prefer those APIs with explicit **label/context** strings so keys for “encrypt” and “MAC” never collide.

### Deriving multiple keys from one secret

```text
root = IKM from KEM / X25519 / KMS
k_enc = HKDF-Expand(..., info="v1/enc", L=32)
k_mac = HKDF-Expand(..., info="v1/mac", L=32)
```

Bind `info` to protocol version and purpose. Rotate the root when compromise is suspected ([5](./5_Key_Management_And_Modules.md)).

### Integrity without confidentiality

Sometimes you only need tamper evidence (software update metadata, audit exports). Options:

| Option | When |
|--------|------|
| HMAC with shared secret | Two-party systems that already share a key |
| Digital signature | Many verifiers; public verify ([3](./3_Asymmetric_Signatures_And_Certificates.md)) |
| AEAD with empty plaintext / GMAC-class | When your stack already standardizes AEAD |
| Hash published out of band | Weak alone if the attacker can also change the published hash channel |

### Side channels (builder minimum)

| Risk | Habit |
|------|-------|
| Timing on tag/password compare | Constant-time compare APIs |
| Logging secrets / IKM | Redact; never log PRK/OKM |
| Error oracles | Same error for “bad MAC” and “bad padding” where applicable |

## Apply

1. Inventory every bare `SHA*` used for “security”; classify hash vs HMAC vs password hash vs KDF.  
2. Replace `H(secret||msg)` MACs with HMAC or AEAD.  
3. Introduce HKDF (or SP 800-108 helper) for any protocol that derives multiple keys from one secret.  
4. Enforce constant-time compares for tags and password verifiers in code review checklists.  
5. Document `info`/label strings next to key-version fields in storage formats.

## Self-check

- [ ] Explain why `SHA256(file)` alone does not authenticate the file  
- [ ] Contrast HMAC and digital signatures (who can forge)  
- [ ] State when HKDF is right and when Argon2id is right  
- [ ] Name one length-extension foot-gun  

## Next

- [Applied patterns: tokens and request signing](./11_Applied_Patterns_Tokens_And_Signing.md)

## Further reading

- [FIPS 198-1 — The Keyed-Hash Message Authentication Code (HMAC)](https://csrc.nist.gov/pubs/fips/198/1/final)
- [NIST SP 800-108 Rev. 1 — Key Derivation Using Pseudorandom Functions](https://csrc.nist.gov/pubs/sp/800/108/r1/final)
- [IETF RFC 5869 — HKDF](https://www.rfc-editor.org/rfc/rfc5869.html)
- [OWASP — Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [NIST Cryptographic Standards and Guidelines](https://csrc.nist.gov/Projects/cryptographic-standards-and-guidelines)
