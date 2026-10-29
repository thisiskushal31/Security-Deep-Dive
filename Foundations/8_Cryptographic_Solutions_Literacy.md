# Cryptographic solutions literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Cryptography supports confidentiality, integrity, authenticity, and non-repudiation when used correctly. This note names the building blocks. Misuse patterns: [Cryptography/1](../Cryptography/1_Practical_Pitfalls.md). TLS on the wire: [Networks Security](https://github.com/thisiskushal31/Networks-Deep-Dive). Design trade-offs: [System-Design Security-Tradeoffs/3](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/3_Encryption_Performance_and_Key_Rotation.md).

## Concept

## What cryptography can provide

| Goal | Common mechanism |
|------|------------------|
| Confidentiality | Encryption |
| Integrity | Hashing; authenticated encryption; signatures |
| Origin authentication / non-repudiation | Digital signatures with protected private keys |
| Password storage at rest | Slow password hashing with salt (not a general-purpose fast hash alone) |

Encryption does not replace authorization. Authorized-but-wrong users can still read decrypted data.

| Term | Meaning |
|------|---------|
| Plaintext / ciphertext | Readable data vs transformed data |
| Cipher / algorithm | The procedure that transforms with a key |
| Key | Secret or key-pair material the algorithm needs |
| Cryptanalysis | Attacking cryptographic protection without already knowing the key |
| Cryptology | Broader field covering cryptography and cryptanalysis |

Everyday transport examples that use cryptography correctly: **HTTPS** (web) and **SSH** (remote admin). Packet and handshake detail: Networks-Deep-Dive.

## Symmetric and asymmetric cryptography

| | Symmetric | Asymmetric |
|--|-----------|------------|
| Keys | Same key for related encrypt/decrypt operations | Key pair (public / private) |
| Strength | Efficient for bulk data | Useful for key establishment and signatures |
| Main risk | Distributing and protecting the shared key | Protecting the private key and trusting the right public keys |

Modern transport security typically uses asymmetric cryptography to establish keys, then symmetric cryptography for bulk data.

## Where encryption is applied

| Level | Protects | Does not replace |
|-------|----------|------------------|
| Full disk | Entire drive when powered off / locked | Attacks after the OS is unlocked |
| Partition / volume | A partition or logical volume | Other partitions left clear |
| File / folder | Selected files | Application bugs after decrypt |
| Database | Database files at rest | Over-broad query permissions |
| Record / field | Specific columns or fields | Key-access abuse; some analytics patterns |
| Transport / communication | Data on the network path | Compromised endpoints |

### Algorithms, key length, and key exchange (literacy)

| Topic | Practice |
|-------|----------|
| Algorithms | Prefer current platform-recommended suites; retire broken or deprecated algorithms on a schedule |
| Key length | Longer keys raise brute-force cost; follow current guidance for the algorithm family you use |
| Key exchange | Establish shared secrets safely (modern TLS does this for you); never invent a home-grown exchange |
| Authenticated encryption | Prefer modes that provide confidentiality and integrity together |

![Hash function for integrity checking](../Assets/Foundations/GFG_Hash_Function.webp)

*Image: [Understanding CIA Triad — integrity / hash function](https://www.geeksforgeeks.org/cybersecurity/the-cia-triad-in-cryptography/).*

## Hashing, salting, stretching, signatures

| Concept | Point |
|---------|-------|
| Hash | One-way fingerprint of data; detects change; not encryption |
| Salt | Per-secret random value so identical passwords do not produce identical stored hashes |
| Key stretching | Intentionally slow password hashing parameters that raise offline cracking cost |
| Digital signature | Private key signs; public key verifies — supports integrity and origin authentication |
| Message authentication code | Integrity/authenticity with a shared secret |

```text
data  →  hash  →  digest
change one bit of data  →  different digest
```

## Advanced

## Public key infrastructure (PKI)

| Piece | Role |
|-------|------|
| Public / private key | Cryptographic identity material |
| Certificate | Binds a public key to an identity, signed by a certification authority (CA) |
| Third-party vs enterprise CA | Who vouches for the binding |
| Trust anchor / root of trust | The starting point of validation |
| Certificate signing request (CSR) | Request to issue a certificate |
| Wildcard / SAN certificates | Name coverage — mis-issuance expands blast radius |
| Revocation (CRL / OCSP) | Learn that a certificate should no longer be trusted |
| Self-signed certificate | Useful in closed labs; dangerous if clients skip validation |
| Key escrow | Organization can recover keys under policy — enables recovery and creates insider/process risk |

## Key protection tools

| Tool | Role |
|------|------|
| TPM | Platform-bound keys and related measurements |
| HSM | Hardened cryptographic module for high-assurance key use |
| Key management system / service | Organizational key lifecycle (create, rotate, retire) |
| Secure enclave | Isolated environment for sensitive operations |

## Obfuscation is not encryption

| Technique | Role |
|-----------|------|
| Tokenization | Replace a sensitive value with a surrogate token in application data |
| Masking | Hide parts of a value for display or support |
| Steganography | Hide data inside other data — niche technique, not a default control |

Encoding (for example Base64) is not confidentiality.

## Ledgers and blockchain (awareness)

Append-oriented public or consortium ledgers can support integrity and transparency use cases for specific records. They do not automatically provide confidentiality, replace key management, or fix application authorization. Treat them as specialized tools, not a default AppSec control.

## Apply
1. Prefer platform TLS and maintained libraries; do not invent protocols.  
2. Prefer authenticated encryption modes recommended by current platform guidance.  
3. Keep keys in a managed store — not in git or container images.  
4. Plan rotation before the first production key.  
5. Validate certificates in clients; do not disable validation in production.

## Self-check

- [ ] Explain why hashing is not encryption  
- [ ] Place disk, transport, and field encryption on one data path  
- [ ] Tell a short PKI story: key pair, certificate, validation, revocation  
- [ ] List two misuse patterns from [Cryptography/1](../Cryptography/1_Practical_Pitfalls.md)  

## Next

- [Change management and security](./9_Change_Management_And_Security.md)

## Further reading

- [NIST SP 800-57 Part 1 — Recommendation for Key Management](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final)
- [NIST SP 800-175B — Guideline for Using Cryptographic Standards](https://csrc.nist.gov/pubs/sp/800/175/b/r1/final)
- [Networks-Deep-Dive Security (TLS)](https://github.com/thisiskushal31/Networks-Deep-Dive)
