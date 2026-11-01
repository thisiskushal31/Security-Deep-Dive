# Cryptographic storage and secrets

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

How to protect **data at rest** and how to handle **secrets** (API keys, private keys, DB passwords) so encryption and vaults actually reduce risk. AEAD: [2](./2_Symmetric_AEAD_And_Modes.md). Key hierarchy: [5](./5_Key_Management_And_Modules.md). Password verifiers: [6](./6_Password_Verifiers_And_KDF.md). Cloud-shaped secrets sprawl also: [Cloud-Security](../Cloud-Security/README.md).

## Concept

### Data at rest vs secrets

| Kind | Examples | Typical control |
|------|----------|-----------------|
| **Data at rest** | DB columns, files, backups, object storage | Disk/volume encryption + application-level AEAD for sensitive fields |
| **Secrets** | Private keys, API tokens, KMS creds, webhook HMAC keys | Vault / KMS / sealed store; short lifetime; never git |

Full-disk encryption protects against lost disks. It does **not** protect data from a compromised OS user or SQL injection that reads decrypted rows. Field/envelope encryption and authZ still matter.

### Layers of encryption (pick intentionally)

```text
[Application / field AEAD]  ← binds to key id + AAD (row id)
[Database TDE / volume]     ← coarse; helps stolen media
[Disk / VM encryption]      ← stolen laptop/disk
[Backup encryption]         ← offline copies often forgotten
```

Missing the backup layer is a classic incident pattern: production encrypted, backups clear in another account.

### Envelope encryption for stored objects

```text
1. KMS GenerateDataKey → DEK plaintext (briefly) + wrapped DEK
2. AEAD-encrypt object with DEK; discard plaintext DEK from memory ASAP
3. Store: { key_id, wrapped_DEK, iv, tag, ciphertext, aad_fields }
4. On read: KMS decrypt wrap → DEK → AEAD-decrypt
```

Application roles call KMS with IAM least privilege. Prefer never persisting plaintext DEKs.

### Secrets management principles

| Principle | Practice |
|-----------|----------|
| **No secrets in source** | Not in git, not in mobile apps, not in frontend |
| **No secrets in images** | Build args and layers leak; inject at runtime |
| **Short lifetime** | Rotate; prefer ephemeral cloud roles over static keys |
| **Least privilege** | One secret per purpose / environment |
| **TLS for distribution** | Never email or ticket plaintext secrets |
| **Detect sprawl** | Scanning + revoke when found |

## Advanced

### What to encrypt at the application layer

| Encrypt | Often skip / minimize |
|---------|------------------------|
| Primary account numbers, government ids, credentials at rest | Public marketing copy |
| Private messages / highly sensitive fields | Fields you must search/sort without cryptography redesign |
| Backup exports of sensitive tables | Data you should not retain |

If you must search encrypted fields, you are in specialized design territory (blind indexes, searchable encryption trade-offs). Do not invent clever crypto for search — change the product requirement or use proven patterns with expert review.

### Associated data for stored ciphertext

Bind ciphertext to **row id**, tenant id, or document version via AAD so attackers cannot swap ciphertext between rows. Store key version explicitly.

### Secret classes and handling

| Class | Handling |
|-------|----------|
| Third-party API keys | Vault; rotate on vendor schedule + on leak |
| DB passwords | Vault + IAM auth when available (avoid static passwords) |
| Private signing keys | HSM/KMS sign APIs; export rarely |
| TLS private keys | Controlled stores; automate issuance (ACME) carefully |
| Webhook HMAC secrets | Per-endpoint secrets; rotate with dual-verify window |

### Anti-patterns that keep showing up

| Anti-pattern | Fix grain |
|--------------|-----------|
| Secrets in CI logs | Masking + OIDC to cloud roles |
| “Dev and prod share the same key” | Separate key rings and accounts |
| Long-lived AK/SK on laptops | Short-lived credentials / SSO |
| Encrypting then storing key beside ciphertext in the same table without wrap | Use KMS wrap / envelope |
| Base64 “encryption” | Not encryption |

### Relationship to supply chain signing

Signing build artifacts (Sigstore/cosign literacy) is delivery integrity — owned with [Cloud-Security/6](../Cloud-Security/6_Supply_Chain_And_Delivery_Integrity.md). This note owns **application secrets and data-at-rest crypto** for product data.

### Multi-tenant key isolation

| Model | Trade-off |
|-------|-----------|
| One KMS key per tenant | Strong isolation; more keys to manage |
| Shared KEK + per-tenant DEK | Common; enforce IAM + AAD binding to `tenant_id` |
| Client-held keys (BYOK) | Customer custody; harder support/recovery |

Always put `tenant_id` in AEAD AAD so ciphertext cannot be moved across tenants unnoticed ([2](./2_Symmetric_AEAD_And_Modes.md)).

### Searchable fields and “blind indexes”

If product requires lookup by encrypted value (email, phone):

```text
store: AEAD(email) + HMAC(pepper, normalize(email)) as lookup index
```

Blind indexes still leak equality and can enable confirmation attacks. Prefer not storing the sensitive value when a hash of a normalized form plus a pepper suffices for join — and get a design review before inventing searchable encryption.

### Backups, replicas, and analytics exports

| Copy | Crypto requirement |
|------|-------------------|
| Automated DB snapshots | Encrypted with keys not stored only on the same volume |
| Cross-region replicas | Key availability in the region without weakening IAM |
| Data-lake exports | Tokenize / exclude secrets; encrypt the drop zone |
| Support “download my data” | AuthZ + short-lived links over TLS; minimize clear extracts |

### Secret sprawl detection loop

```text
prevent (vault + CI block) → detect (secret scan) → rotate → revoke → retrospect
```

Finding a secret in git without rotating it is only half a fix — assume exposure from the first commit that contained it.

## Apply

1. Classify stored data: public / internal / sensitive / restricted; map encryption layer per class.  
2. Implement envelope encryption for one sensitive object store end-to-end with key versioning.  
3. Move all static secrets to a vault; delete from repos/history where feasible (rotate after history purge).  
4. Encrypt backups with keys that are not stored on the same backup media.  
5. Add secret scanning to CI and centralize revoke runbooks.

## Self-check

- [ ] Contrast disk encryption vs field AEAD  
- [ ] Draw envelope encryption with wrapped DEK  
- [ ] List five places secrets must not live  
- [ ] Explain why backups need their own crypto story  

## Next

- [TLS and PKI builder pitfalls](./8_TLS_And_PKI_Builder_Pitfalls.md)

## Further reading

- [OWASP — Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [OWASP — Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
- [OWASP — Key Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html)
- [NIST SP 800-57 Part 1 Rev. 5 — Key Management](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final)
