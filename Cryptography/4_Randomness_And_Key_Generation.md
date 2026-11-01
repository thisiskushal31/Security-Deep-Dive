# Randomness and key generation

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

Cryptography collapses when “random” is guessable. This note is how builders get **keys, nonces, IVs, salts, and tokens** from a CSPRNG (Cryptographically Secure Pseudorandom Number Generator) — and how NIST (National Institute of Standards and Technology) frames DRBG (Deterministic Random Bit Generator) and key generation. Modes that consume nonces: [2](./2_Symmetric_AEAD_And_Modes.md). After keys exist: [5](./5_Key_Management_And_Modules.md).

## Concept

### Why randomness is a security control

| Use | If randomness fails |
|-----|---------------------|
| Encryption keys | Attacker predicts or brute-forces the key space you accidentally shrunk |
| GCM IV (Initialization Vector) / AEAD (Authenticated Encryption with Associated Data) nonce | IV reuse under one key → catastrophic integrity/confidentiality failures |
| Password salts | Parallel attacks and precomputation become easier |
| Session tokens / API keys | Account takeover by prediction |
| Key agreement ephemeral values | Long-term key compromise or impersonation (protocol-dependent) |

Ordinary PRNGs designed for simulations (`Math.random`, libc `rand`, time-seeded generators) are **not** CSPRNGs. Do not seed cryptographic generators with timestamps, PIDs, or MAC (Message Authentication Code) addresses alone.

### CSPRNG vs DRBG (builder view)

| Term | Meaning in practice |
|------|---------------------|
| **CSPRNG** | Generator suitable for cryptographic secrets; unpredictable to attackers who see prior outputs |
| **DRBG** | NIST term for approved deterministic generators (SP 800-90A) seeded from sufficient entropy |
| **Entropy source** | Hardware/OS noise that seeds or reseed the generator |

Product code should almost always call the **OS or language CSPRNG** (`getrandom` / `SecureRandom` / `secrets` module / Web Crypto `getRandomValues`) or a library that wraps it — not implement SP 800-90A from scratch.

```text
entropy (OS / hardware)
        │
        ▼
   CSPRNG / DRBG
        │
        ├── keys
        ├── nonces / IVs
        ├── salts
        └── high-entropy tokens
```

### Generating keys

NIST SP 800-133 frames cryptographic key generation: use approved methods so key material has the intended strength. Builder habits:

| Do | Don’t |
|----|-------|
| Use library `generate_key` / KMS (Key Management Service) CreateKey | Derive keys from passwords without a password-based KDF (Key Derivation Function) designed for that job |
| Use full-entropy keys for AES (128/256-bit) | Truncate hashes of low-entropy strings and call them keys |
| Let KMS/HSM (Hardware Security Module) generate and never export raw keys when possible | Copy raw key bytes into tickets and chat |
| Generate a fresh nonce per message | Reuse a counter incorrectly across processes without coordination |

Password-based keys for storage encryption are a special case — use an approved PBKDF (Password-Based Key Derivation Function) with a salt ([6](./6_Password_Verifiers_And_KDF.md)); prefer Argon2id-class memory-hard functions for *password verifiers*, and follow current guidance when deriving encryption keys from passphrases.

## Advanced

### Tokens are not keys (but still need CSPRNG)

Session identifiers and API tokens must be unpredictable. Use CSPRNG bytes encoded as hex/base64url. Do not use sequential IDs, emails, or JWT (JSON Web Token) payloads alone as bearer secrets.

### Nonce construction patterns

| Pattern | Notes |
|---------|-------|
| Random 96-bit IV for AES-GCM (Advanced Encryption Standard in Galois/Counter Mode) | Common; store IV with ciphertext; watch volume limits under one key |
| Counter / message-id nonce | Safe only if uniqueness is guaranteed across all writers sharing the key |
| “Nonce = hash(message)” | Dangerous if messages can collide or be chosen; do not invent this |

When multiple hosts share one key, **centralize** nonce allocation or use random IVs with key rotation — do not assume uncoordinated counters stay unique.

### Fork / VM / snapshot hazards

Virtual machine clones and process forks can duplicate RNG state if the stack mishandles it. Prefer OS interfaces known to reseed across fork (modern `getrandom` usage) and treat golden-image cloning as a key-and-nonce risk review item.

### Deterministic tests vs production

| Environment | Habit |
|-------------|--------|
| Unit tests | May inject a test double for crypto RNG **only in test builds** |
| Production | Never ship a build flag that swaps in a weak RNG |
| Fixtures | Store known-answer test vectors from official docs; do not hard-code live keys |

### Strength and “bits of security”

Key length and algorithm family set a security-strength story (see transition tables in [9](./9_Algorithm_Transition_And_PQC.md)). Generating a 256-bit AES key from a 40-bit memorable secret without stretching does not give you “AES-256 security.”

### After you have key material: derive, don’t hash-hack

Once you have high-entropy IKM (Input Keying Material) from a KEM (key-encapsulation mechanism), Diffie–Hellman, or KMS:

| Need | Use |
|------|-----|
| Many purpose-bound keys | **HKDF (HMAC-based Key Derivation Function)** / SP 800-108 helpers with distinct `info` labels — [10](./10_Hashing_MACs_And_Key_Derivation.md) |
| Password → key | Password KDF — [6](./6_Password_Verifiers_And_KDF.md) |
| One-off AES key | CSPRNG `generate_key` / KMS GenerateDataKey |

### UUIDs, “random” strings, and security theater

| Generator | Security use? |
|-----------|----------------|
| UUIDv4 from CSPRNG | Often OK as opaque ids if implementation is CSPRNG-backed |
| UUIDv1 / time-based | Not a secret |
| `random.choice` on letters without crypto RNG | Not a secret |
| Short numeric OTPs alone | Need rate limits + transport auth; not high-entropy keys |

### Boot-time and container entropy

Containers and embedded devices can start with a thin entropy pool. Prefer kernel interfaces that block until initialized (`getrandom` with proper flags / language wrappers that do). For golden images, regenerate machine identities and never bake private keys into the image ([7](./7_Cryptographic_Storage_And_Secrets.md)).

## Apply

1. Inventory every secret generation site; confirm CSPRNG APIs.  
2. Ban `Math.random` / time seeds for security tokens in lint or code review checklists.  
3. For AEAD, verify IV/nonce is library-generated or uniqueness-proof; log key id + never log raw IV+key together in clear debug dumps that leave the box.  
4. Prefer KMS/HSM GenerateDataKey patterns so raw DEKs are short-lived in memory.  
5. Add a chaos/review item for VM clone and horizontal-scaling nonce coordination.

## Self-check

- [ ] Define CSPRNG in one sentence and give two unsafe substitutes  
- [ ] Explain why GCM IV reuse is an RNG/operations problem as much as a math problem  
- [ ] Separate “password as key” from “password verifier hashing”  
- [ ] Know where your language documents SecureRandom / secrets  

## Next

- [Key management and cryptographic modules](./5_Key_Management_And_Modules.md)

## Further reading

- [NIST SP 800-90A Rev. 1 — DRBG Mechanisms](https://csrc.nist.gov/pubs/sp/800/90/a/r1/final)
- [NIST SP 800-133 Rev. 2 — Recommendation for Cryptographic Key Generation](https://csrc.nist.gov/pubs/sp/800/133/r2/final)
- [NIST SP 800-57 Part 1 Rev. 5 — Key Management (General)](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final)
- [OWASP — Key Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html)
