# Practical cryptography pitfalls

[← Cryptography](./README.md)

Engineers break crypto more often by **misuse** than by breaking AES. Use platform libraries and managed services; do not invent protocols.

## Do / don't

| Do | Don't |
|----|-------|
| Use TLS 1.2+ with modern ciphers (platform defaults) | Roll your own TLS or disable verification “temporarily” |
| Use vetted libs (libsodium, language crypto APIs) | Copy Stack Overflow cipher modes without AEAD |
| Prefer AEAD (AES-GCM, ChaCha20-Poly1305) | ECB; raw CBC without HMAC design |
| Managed KMS / HSM for keys | Hard-code keys; commit PEM private keys |
| Random from CSPRNG | `Math.random` / time-based seeds for secrets |
| Password hashing: Argon2id / bcrypt / scrypt | Fast hashes (SHA-256 alone) for passwords |

## Common product failures

| Failure | Fix grain |
|---------|-----------|
| Secrets in images or repos | Handbook secrets + Gitleaks / Vault |
| Long-lived signing keys everywhere | Rotate; split signing vs encryption keys |
| “Encrypt then put ciphertext in URL forever” | Short TTL; valet-key patterns |
| Mixed HTTP/HTTPS or wrong redirect | HSTS; fix mixed content |

## Depth elsewhere

- Wire TLS → [Networks Encryption/TLS](https://github.com/thisiskushal31/Networks-Deep-Dive/blob/main/Security/2_Encryption_Tls.md)
- Architecture trade-offs → [System-Design Encryption tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/3_Encryption_Performance_and_Key_Rotation.md)
- SSL/TLS pattern note → [System-Design Security/6](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security/6_SSL_and_TLS.md)

This folder is **pitfalls and policy**, not a math course.
