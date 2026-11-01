# Goals, threat model, and misuse

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

Why cryptography exists in products, what it **cannot** replace, and why most “crypto failures” are **misuse** — wrong goals, home-grown protocols, disabled checks — not someone factoring AES (Advanced Encryption Standard). Vocabulary seed: [Foundations/8](../Foundations/8_Cryptographic_Solutions_Literacy.md). Modes and AEAD (Authenticated Encryption with Associated Data) depth: [2](./2_Symmetric_AEAD_And_Modes.md). Keys: [4](./4_Randomness_And_Key_Generation.md) · [5](./5_Key_Management_And_Modules.md).

## Concept

### What cryptography is for

Cryptography is a set of mechanisms that, when used correctly, support security *properties* for data and protocols:

| Property | Everyday meaning | Typical mechanisms |
|----------|------------------|--------------------|
| **Confidentiality** | Unauthorized parties cannot read the data | Encryption (symmetric or hybrid) |
| **Integrity** | Unauthorized modification is detectable | Hash + signature/MAC (Message Authentication Code); AEAD |
| **Authenticity** | You can check origin / binding of data or a peer | Signatures; MAC with shared secret; TLS (Transport Layer Security) certificates |
| **Non-repudiation** (qualified) | A party cannot credibly deny an action *if* key custody and logging hold | Digital signatures + protected private keys + audit trail |

These are **not** the same as authorization. Encryption does not decide whether *Alice* may open *Bob’s* record. If the app decrypts for any logged-in user, cryptography did its job and the product still leaked.

### What cryptography is not

| Myth | Reality |
|------|---------|
| “We encrypt, so we’re secure” | Crypto is one control class; authZ, config, and ops still dominate breaches |
| “Hashing = encryption” | Hashes are one-way fingerprints; they do not keep secrets confidential |
| “Encoding / obfuscation = crypto” | Base64, scrambling, and steganography are not confidentiality controls |
| “TLS everywhere means data is safe at rest” | Transport protection ends at the endpoint; disk/DB/backups need their own design |
| “Strong algorithm fixes bad key handling” | Stolen keys, reused nonces, and disabled validation defeat strong algorithms |

```text
Threat → finds weakness in *how* crypto is used
              │
              ├─ wrong property (encrypt without integrity)
              ├─ wrong primitive (SHA-256 as password hash)
              ├─ wrong parameters (ECB, static IV, short key)
              ├─ wrong custody (key in git / image)
              └─ wrong trust decision (skip cert validation)
```

### Builder threat model (minimum)

Before picking AES or RSA (Rivest–Shamir–Adleman), name the adversary and the asset:

| Question | Forces a decision |
|----------|-------------------|
| What must stay confidential? | Field, file, disk, backup, or only on the wire? |
| Who may decrypt / verify? | Service identity, human role, offline recovery role? |
| What happens if ciphertext is flipped? | Need AEAD / signature — not “encryption alone” |
| Where do keys live when the box is compromised? | Memory, HSM (Hardware Security Module), KMS (Key Management Service), laptop? |
| How long must confidentiality hold? | Affects cryptoperiod, algorithm choice, and PQC (post-quantum cryptography) planning ([9](./9_Algorithm_Transition_And_PQC.md)) |

If you cannot answer those, you are decorating the system with algorithms, not designing a control.

### The first rule: do not invent cryptography

Use **maintained, high-level libraries** and platform services (language crypto APIs, libsodium-class constructions, cloud KMS, OS TLS stacks). Low-level “assemble AES yourself” interfaces exist for experts and for interoperability — they are foot-guns for product teams.

| Do | Don’t |
|----|-------|
| Call AEAD encrypt/decrypt APIs that take plaintext + associated data | Copy Stack Overflow CBC (Cipher Block Chaining) + homemade HMAC (Hash-based Message Authentication Code) glue |
| Use the platform TLS stack with modern defaults | Roll a custom “secure channel” over TCP |
| Store passwords with a password-hashing API | `SHA256(password)` or encrypt passwords “so we can decrypt them” |
| Generate keys with the library’s keygen / OS CSPRNG (Cryptographically Secure Pseudorandom Number Generator) | `Math.random`, timestamps, or “random” UUIDs as key material |

## Advanced

### Failure classes you will actually see

| Class | Symptom in products | Deeper note |
|-------|---------------------|-------------|
| **Confidentiality without integrity** | Ciphertext malleable; attacker flips bits or truncates | [2](./2_Symmetric_AEAD_And_Modes.md) |
| **Integrity without authenticity intent** | Hash of file “for security” with no key or signature | [3](./3_Asymmetric_Signatures_And_Certificates.md) |
| **Weak or reused randomness** | Predictable tokens, colliding IVs (Initialization Vectors) | [4](./4_Randomness_And_Key_Generation.md) |
| **Key custody failure** | PEM in repo; shared prod/dev key; forever cryptoperiod | [5](./5_Key_Management_And_Modules.md) |
| **Password storage failure** | Fast hashes; unsalted; reversible encryption of passwords | [6](./6_Password_Verifiers_And_KDF.md) |
| **Secrets sprawl** | API keys in images, CI logs, tickets | [7](./7_Cryptographic_Storage_And_Secrets.md) |
| **Transport misuse** | TLS 1.0; verification off; mixed content | [8](./8_TLS_And_PKI_Builder_Pitfalls.md) |
| **Algorithm debt** | SHA-1 signatures; 1024-bit RSA; no migration plan | [9](./9_Algorithm_Transition_And_PQC.md) |

### Confidentiality is not a substitute for minimization

The cheapest cryptographic win is **not storing** sensitive data. Classify data; keep what the business needs; tokenize or drop the rest. Encrypting a warehouse of unnecessary PII (Personally Identifiable Information) still creates key-compromise and insider risk.

### Encrypt-then-MAC vs AEAD (product view)

Older designs sometimes encrypt, then compute a MAC over the ciphertext (encrypt-then-MAC). Modern default for new work: **AEAD modes** (AES-GCM (Advanced Encryption Standard in Galois/Counter Mode), ChaCha20-Poly1305, AES-CCM) that provide confidentiality and authenticity in one construction when used as the library documents — especially IV (Initialization Vector)/nonce uniqueness.

```text
Prefer for new designs:

  plaintext + AAD  ──AEAD──▶  ciphertext || tag

Avoid reinventing:

  plaintext ──ENC──▶ C ──MAC──▶ tag     (easy to get order/AD wrong)
```

Associated authenticated data (**AAD (additional authenticated data)**) is data you need integrity for but not secrecy (for example a record id or version header). Put stable identifiers in AAD so ciphertext cannot be relocated across rows unnoticed.

### Program placement (where this folder sits)

| Concern | Owner |
|---------|--------|
| “Should this endpoint even return the field?” | AppSec / product authZ |
| “Is the TLS handshake correct on the wire?” | Networks depth |
| “Which AEAD, how do we rotate DEKs?” | **This folder** |
| “Is the cloud KMS policy least-privilege?” | Cloud-Security + IAM (Identity and Access Management) |

### Cryptographic inventory (so you can migrate)

You cannot rotate, deprecate, or go post-quantum what you cannot find. Keep a living inventory of:

| Track | Examples |
|-------|----------|
| **Algorithms / modes** | AES-GCM, RSA-PSS, HMAC-SHA-256, Argon2id |
| **Keys & certs** | KMS key ids, TLS certs, code-signing keys, JWT signing keys |
| **Libraries** | OpenSSL/BoringSSL, language crypto, libsodium, cloud KMS SDKs |
| **Where used** | Service, data class, cryptoperiod, owner |

This matches the spirit of OWASP ASVS (Application Security Verification Standard) V11 inventory/documentation requirements. Discovery (secret scanning, library inventories, cert inventories) feeds the sheet; migration plans live with [9](./9_Algorithm_Transition_And_PQC.md).

### Map to OWASP (Open Worldwide Application Security Project) Top 10:2025 A04 — Cryptographic Failures

**A04 Cryptographic Failures** covers weak algorithms, broken key management, missing or botched TLS, secrets in source, and homemade crypto — typically leading to sensitive data exposure or compromise. Use this folder as the control depth behind AppSec’s A04 pointer.

| A04-shaped failure | Start here |
|--------------------|------------|
| Homemade or low-level misuse | This note · [2](./2_Symmetric_AEAD_And_Modes.md) |
| Broken public-key / cert trust | [3](./3_Asymmetric_Signatures_And_Certificates.md) · [8](./8_TLS_And_PKI_Builder_Pitfalls.md) |
| Predictable secrets / tokens | [4](./4_Randomness_And_Key_Generation.md) |
| Keys in git / no rotation | [5](./5_Key_Management_And_Modules.md) · [7](./7_Cryptographic_Storage_And_Secrets.md) |
| Fast password hashes | [6](./6_Password_Verifiers_And_KDF.md) |
| Missing TLS / validation off | [8](./8_TLS_And_PKI_Builder_Pitfalls.md) |
| Deprecated algorithms | [9](./9_Algorithm_Transition_And_PQC.md) |
| Bare hash as “MAC”; bad KDF (Key Derivation Function) choice | [10](./10_Hashing_MACs_And_Key_Derivation.md) |
| JWT (JSON Web Token) `alg` confusion / webhook HMAC bugs | [11](./11_Applied_Patterns_Tokens_And_Signing.md) |

![OWASP Top 10:2025 category mapping context](../Assets/AppSec/OWASP_Top10_2025_Mappings.png)

*Credit: OWASP Top 10:2025 — category mapping figure (A04 Cryptographic Failures lineage).*

## Apply

1. Write a one-page threat note per sensitive data store: asset, adversary, property needed (confidentiality / integrity / authenticity), key owner, rotation trigger.  
2. Ban new use of raw block-cipher modes without AEAD or an explicit Encrypt-then-MAC design reviewed by someone who can name the forgery risk.  
3. Inventory where keys and passwords live (git, images, env files, tickets). Move them before adding more encryption.  
4. Prefer one vetted library/stack per language runtime; document the approved API surface for teams.  
5. Add CI checks for private key material and high-entropy secrets in repos (process lives with secrets hygiene in [7](./7_Cryptographic_Storage_And_Secrets.md)).

## Self-check

- [ ] Explain why encryption does not replace authorization  
- [ ] Name three misuse classes that defeat a strong algorithm  
- [ ] State the first rule for product teams (library / service vs home-grown)  
- [ ] Distinguish hash, MAC, signature, and AEAD in one sentence each  

## Next

- [Symmetric crypto, AEAD, and modes](./2_Symmetric_AEAD_And_Modes.md)

## Further reading

- [OWASP — Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [OWASP Top 10 Proactive Controls — C2 Use Cryptography to Protect Data](https://top10proactive.owasp.org/the-top-10/c2-crypto/)
- [OWASP Top 10:2025](https://owasp.org/Top10/2025/) (A04 Cryptographic Failures)
- [NIST SP 800-175B Rev. 1 — Guideline for Using Cryptographic Standards (Mechanisms)](https://csrc.nist.gov/pubs/sp/800/175/b/r1/final)
- [libsodium documentation](https://doc.libsodium.org/)
