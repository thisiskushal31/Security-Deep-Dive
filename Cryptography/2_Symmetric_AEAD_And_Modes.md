# Symmetric crypto, AEAD, and modes

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

How bulk encryption works in products: a shared secret key, a block cipher (usually AES — Advanced Encryption Standard), a **mode of operation**, and why modern systems prefer **AEAD** constructions. Goals/misuse frame: [1](./1_Goals_Threat_Model_And_Misuse.md). Key generation and custody: [4](./4_Randomness_And_Key_Generation.md) · [5](./5_Key_Management_And_Modules.md).

## Concept

### Symmetric encryption in one picture

Symmetric cryptography uses the **same key** for related encrypt and decrypt operations. It is the workhorse for bulk data once a key is established (often via TLS or a KMS data key).

```text
shared key K
    │
    ├─ encrypt(K, plaintext, IV/nonce, AAD?) → ciphertext (+ tag if AEAD)
    └─ decrypt(K, ciphertext, IV/nonce, AAD?, tag?) → plaintext or FAIL
```

| Piece | Role |
|-------|------|
| **Algorithm** | AES (FIPS 197) is the default block cipher in federal and industry guidance |
| **Key length** | AES-128 or AES-256 in practice; follow current platform/policy tables ([9](./9_Algorithm_Transition_And_PQC.md)) |
| **Mode** | How the block cipher is applied to messages longer than one block |
| **IV / nonce** | Per-message input that must meet the mode’s uniqueness/randomness rules |
| **Tag** (AEAD) | Authentication tag; decrypt must verify before returning plaintext |

### Why “AES” alone is not a design

Saying “we use AES” is incomplete. AES is a **block cipher**: it encrypts fixed-size blocks. Real messages need a **mode**. Modes differ sharply in integrity guarantees and IV rules.

| Mode family | Confidentiality | Integrity / authenticity | Typical product stance |
|-------------|-----------------|--------------------------|------------------------|
| **ECB (Electronic Codebook)** | Weak for structured data (patterns leak) | None | Avoid outside narrow, expert-reviewed cases |
| **CBC (Cipher Block Chaining)** | Yes (with random IV) | None by itself | Legacy; if used, needs Encrypt-then-MAC and careful padding handling |
| **CTR (Counter)** | Yes (with unique counter/nonce) | None by itself | Prefer upgrading to AEAD rather than bolting MAC on |
| **GCM (Galois/Counter Mode)** | Yes | Yes (AEAD) | Default authenticated mode with AES in many stacks |
| **CCM** | Yes | Yes (AEAD) | Common in constrained / IoT stacks |
| **ChaCha20-Poly1305** | Yes | Yes (AEAD) | Stream AEAD; strong default in many modern libraries |

Authenticated modes (GCM, CCM, ChaCha20-Poly1305) should be the **first preference** for new application storage and custom protocols. Prefer library defaults over hand-rolled combinations.

### AEAD inputs you must get right

```text
AEAD-Encrypt:
  inputs:  key, nonce/IV, plaintext, AAD
  outputs: ciphertext, tag

AEAD-Decrypt:
  inputs:  key, nonce/IV, ciphertext, AAD, tag
  outputs: plaintext  OR  authentication failure (no silent garbage plaintext)
```

| Input | Discipline |
|-------|------------|
| **Key** | From CSPRNG / KMS; never hard-coded; rotate per policy ([5](./5_Key_Management_And_Modules.md)) |
| **Nonce / IV** | Unique per key for GCM (and never reused); prefer library helpers that generate them |
| **AAD** | Headers / identifiers that must be bound to the ciphertext but need not be secret |
| **Tag** | Store and transmit with ciphertext; verify on decrypt |

GCM’s security collapses badly if the **same key and IV** are reused for two distinct encryptions. That is not a theoretical footnote — it is an operational requirement.

## Advanced

### ECB: why patterns survive

ECB encrypts each block independently. Identical plaintext blocks → identical ciphertext blocks. Structured data (disk images, low-entropy records) can leak layout even without key recovery. Do not use ECB for general message encryption.

### CBC and padding oracles (why legacy hurts)

CBC needs a fresh, unpredictable IV per message and careful padding. Historical **padding oracle** classes taught an industry lesson: confidentiality-only modes plus attacker-visible error channels become decryption oracles. If you must touch CBC, treat integrity as mandatory and prefer migrating to AEAD.

### GCM operational limits (builder literacy)

NIST SP 800-38D specifies GCM/GMAC behavior and stresses IV uniqueness. Practical habits:

| Habit | Why |
|-------|-----|
| Prefer **96-bit** IVs when the stack defaults to them | Common, well-supported construction |
| Let the library generate the IV; store it with the ciphertext | Avoids accidental reuse from “fixed IV” bugs |
| Bound how many messages you encrypt under one key | Collision risk grows with volume; rotate DEKs (Data Encryption Keys) |
| On decrypt failure, fail closed | Do not return partial plaintext after a bad tag |

Exact volume limits depend on IV construction (random vs invoking counter). When in doubt, rotate keys more often rather than arguing about \(2^{32}\) edge cases in app code.

### ChaCha20-Poly1305

ChaCha20-Poly1305 is an AEAD construction widely deployed (including inside TLS 1.3 cipher suites). For application-level encryption, use a maintained API (for example libsodium’s secretbox-class constructions or language AEAD interfaces) — not a partial port of ChaCha alone without Poly1305.

### Stream / “XOR with keystream” foot-guns

CTR and stream ciphers turn a block cipher into a keystream. **Never reuse** a key+nonce pair: keystream reuse enables trivial plaintext recovery from ciphertext XOR. AEAD wrappers exist to make the safe path the default — use them.

### Choosing for product storage

| Scenario | Lean toward |
|----------|-------------|
| New app field / envelope encryption | AES-GCM or ChaCha20-Poly1305 via high-level API |
| Interop with existing CBC ciphertext | Decrypt legacy under controlled path; re-encrypt to AEAD on write |
| Extremely constrained device with CCM already validated | AES-CCM via the platform crypto module |
| “We need random access to huge encrypted blobs” | Design carefully (chunked AEAD with unique nonces per chunk); do not invent a new mode |

### Symmetric vs password hashing (do not confuse)

Symmetric encryption is **reversible** with the key. Password storage must be **non-reversible** under normal operation — slow hashes, not AES of the password ([6](./6_Password_Verifiers_And_KDF.md)).

### Encrypt-then-MAC when AEAD is unavailable

If a platform forces CBC/CTR without AEAD:

| Rule | Detail |
|------|--------|
| Order | **Encrypt-then-MAC** over ciphertext (+ length/headers as needed) |
| Keys | Separate MAC key from encryption key (or cleanly derived distinct keys) |
| Verify | Check MAC **before** decrypt; fail closed |
| Variable length | Bind lengths; naive MAC-then-encrypt and MAC-over-plaintext designs are classic foot-guns |

Treat this as a migration bridge. New designs should still aim for AEAD.

### Multi-message / multi-host systems

| Risk | Mitigation |
|------|------------|
| Two services share one AES-GCM key and both pick random IVs | Track volume; rotate DEKs; consider per-host DEKs |
| Log files print IV + ciphertext + key id | Fine; never print the DEK |
| Partial writes / truncated ciphertext | AEAD tag fails — do not “best effort” decrypt |
| Compression before encryption | Can leak via length; be careful with attacker-controlled plaintext |

### Worked mental model (store one field)

```text
aad = tenant_id || record_id || field_name || key_version
iv  = CSPRNG(96 bits)
(ct, tag) = AES-GCM-Encrypt(DEK, iv, plaintext, aad)
persist: key_version, iv, ct, tag
```

On read, rebuild the same AAD; any row-swap or field-swap fails authentication.

## Apply

1. For every encrypt call site, name mode, IV/nonce source, and where the tag is stored.  
2. Grep for ECB, “AES/ECB”, static IVs, and CBC without MAC; open migration tickets.  
3. Standardize on one AEAD API per language; wrap it so product code never touches raw AES blocks.  
4. Pair each DEK with a cryptoperiod and a max-message budget; rotate through KMS/envelope patterns ([5](./5_Key_Management_And_Modules.md), [7](./7_Cryptographic_Storage_And_Secrets.md)).  
5. Add decrypt-failure metrics (auth tag fail) — spikes can mean probing or key mismatch after rotation.

## Self-check

- [ ] Explain why “we use AES” is an incomplete design statement  
- [ ] Contrast ECB, CBC, and GCM for integrity  
- [ ] State the GCM IV uniqueness rule in one sentence  
- [ ] Know when ChaCha20-Poly1305 is a reasonable AEAD choice  

## Next

- [Asymmetric crypto, signatures, and certificates](./3_Asymmetric_Signatures_And_Certificates.md)

## Further reading

- [FIPS 197 — Advanced Encryption Standard (AES)](https://csrc.nist.gov/pubs/fips/197/final)
- [NIST SP 800-38A — Block Cipher Modes of Operation](https://csrc.nist.gov/pubs/sp/800/38/a/final)
- [NIST SP 800-38D — GCM and GMAC](https://csrc.nist.gov/pubs/sp/800/38/d/final)
- [NIST AES-GCM examples (worked vectors)](https://csrc.nist.gov/csrc/media/projects/cryptographic-standards-and-guidelines/documents/examples/aes_gcm.pdf)
- [OWASP — Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [Hashing, MACs, and key derivation](./10_Hashing_MACs_And_Key_Derivation.md) (when AEAD is unavailable and you need Encrypt-then-MAC)
