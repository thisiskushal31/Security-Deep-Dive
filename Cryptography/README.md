# Cryptography

[← README](../README.md)

Builder-depth cryptography for engineers who **ship systems**: what cryptography provides, how it fails when misused (including OWASP Top 10:2025 **A04 Cryptographic Failures**), how to choose algorithms and modes, hashing/MAC/HKDF, how to generate and manage keys, how to store passwords and data at rest, how TLS and certificates break in products, applied token/request-signing patterns, and how to plan algorithm transition including post-quantum readiness.

Vocabulary first pass: [Foundations/8](../Foundations/8_Cryptographic_Solutions_Literacy.md). Wire / handshake depth: [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive). Architecture trade-offs (perf vs crypto): [System-Design Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs). App control placement: [AppSec](../AppSec/README.md) (A04 points here). Cloud KMS product paths: [Cloud-Security](../Cloud-Security/README.md) · [DevOps-Handbook Cloud](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Cloud).

This folder is **not** a number-theory course. Prefer platform libraries and managed services; do not invent protocols.

Each note follows **concept → advanced → apply**. Official Further reading only. Lab/adversarial work stays **authorized and isolated** — [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

```text
1 Goals, threat model & misuse (A04 map)
  → 2 Symmetric AEAD & modes
    → 3 Asymmetric, signatures & certificates
      → 4 Randomness & key generation
        → 5 Key management & modules
          → 6 Password verifiers & KDF
            → 7 Cryptographic storage & secrets
              → 8 TLS & PKI builder pitfalls
                → 9 Algorithm transition & PQC
                  → 10 Hashing, MACs & key derivation
                    → 11 Applied patterns: tokens & signing
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [Goals, threat model, and misuse](./1_Goals_Threat_Model_And_Misuse.md) |
| 2 | [Symmetric crypto, AEAD, and modes](./2_Symmetric_AEAD_And_Modes.md) |
| 3 | [Asymmetric crypto, signatures, and certificates](./3_Asymmetric_Signatures_And_Certificates.md) |
| 4 | [Randomness and key generation](./4_Randomness_And_Key_Generation.md) |
| 5 | [Key management and cryptographic modules](./5_Key_Management_And_Modules.md) |
| 6 | [Password verifiers and key derivation](./6_Password_Verifiers_And_KDF.md) |
| 7 | [Cryptographic storage and secrets](./7_Cryptographic_Storage_And_Secrets.md) |
| 8 | [TLS and PKI builder pitfalls](./8_TLS_And_PKI_Builder_Pitfalls.md) |
| 9 | [Algorithm transition and post-quantum readiness](./9_Algorithm_Transition_And_PQC.md) |
| 10 | [Hashing, MACs, and key derivation](./10_Hashing_MACs_And_Key_Derivation.md) |
| 11 | [Applied patterns: tokens and request signing](./11_Applied_Patterns_Tokens_And_Signing.md) |

## Learning path

`1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11`

Skip ahead only if you already use authenticated encryption and managed keys daily — still read **1** and **5** before production decisions. Read **10** before inventing MACs or key schedules; read **11** before shipping JWT/webhook crypto.

## Topic map

| Area | Home |
|------|------|
| What crypto can / cannot do; threat model; A04 map; “use a library” | 1 |
| AES, modes, GCM / ChaCha20-Poly1305, IV/nonce discipline | 2 |
| Public-key encrypt vs sign; hybrid designs; certificates as bindings | 3 |
| CSPRNG / DRBG; generating keys correctly; entropy in containers | 4 |
| Lifecycle, cryptoperiods, KMS/HSM, FIPS 140-3 literacy | 5 |
| Argon2id / bcrypt / scrypt / PBKDF2; salt / pepper | 6 |
| At-rest encryption; envelope; tenants; backups; secrets sprawl | 7 |
| TLS 1.3/1.2; validation; HSTS; CT/pinning; ACME | 8 |
| Deprecation transitions; ML-KEM / ML-DSA / SLH-DSA readiness | 9 |
| Hash vs HMAC vs HKDF vs password KDF; constant-time compares | 10 |
| JWT/JWE, webhook HMAC, crypto agility (`kid`) | 11 |

## Related homes (do not duplicate)

| Topic | Home |
|-------|------|
| CIA / first crypto vocabulary | [Foundations/8](../Foundations/8_Cryptographic_Solutions_Literacy.md) |
| TLS packet / lab depth | [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive) |
| Encryption vs latency / key rotation at design time | [System-Design Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs) |
| AuthZ failures that encryption cannot fix | [AppSec/3](../AppSec/3_Access_Control_And_Authentication.md) |
| Top 10 A04 pointer | [AppSec/2](../AppSec/2_OWASP_Top_10_2025_In_Practice.md) |
| Cloud identity / data controls / KMS in cloud estates | [Cloud-Security/2](../Cloud-Security/2_Identity_Network_And_Data_Controls.md) |
| Signing delivery artifacts | [Cloud-Security/6](../Cloud-Security/6_Supply_Chain_And_Delivery_Integrity.md) |
