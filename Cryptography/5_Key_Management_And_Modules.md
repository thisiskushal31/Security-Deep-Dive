# Key management and cryptographic modules

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

Keys are the crown jewels of cryptographic controls. This note is the **lifecycle**: generate, distribute, store, use, rotate, revoke, destroy — plus what KMS (Key Management Service), HSM (Hardware Security Module), and FIPS (Federal Information Processing Standard) 140-3 (Federal Information Processing Standard) module validation mean for builders. Randomness: [4](./4_Randomness_And_Key_Generation.md). Using keys for storage envelopes: [7](./7_Cryptographic_Storage_And_Secrets.md).

## Concept

### Why key management dominates outcomes

A perfect AEAD (Authenticated Encryption with Associated Data) implementation fails open if:

- the key is in a public container image,  
- every environment shares one key,  
- there is no rotation after staffing changes, or  
- plaintext key material is readable by every microservice account.

NIST (National Institute of Standards and Technology) SP 800-57 Part 1 is the general key-management recommendation: key types, protection requirements, cryptoperiods, and lifecycle functions. You do not memorize every table — you implement a **system** that matches your assurance needs.

### Lifecycle

```text
Generate → Distribute / wrap → Store → Use → Rotate / replace
                                      ↓
                                 Revoke / destroy
```

| Phase | Builder questions |
|-------|-------------------|
| **Generate** | CSPRNG (Cryptographically Secure Pseudorandom Number Generator) or KMS/HSM? Exportable? |
| **Distribute** | How does the key reach the service without chat/email? |
| **Store** | Vault / KMS / HSM / OS keystore — never git |
| **Use** | Which identities may encrypt, decrypt, sign? Logged? |
| **Rotate** | Time-based, volume-based, and compromise-based triggers |
| **Destroy** | Secure deletion / scheduled disable of old versions |

### Key types you will name in designs

| Type | Role |
|------|------|
| **DEK (Data Encryption Key)** | Encrypts data (often short-lived, many of them) |
| **KEK (Key Encryption Key)** | Encrypts / wraps DEKs |
| **Signing key** | Creates signatures; verify with public key or shared secret for MAC (Message Authentication Code) |
| **Master / root keys** | Protect KEKs; highest custody (HSM / cloud KMS root) |

Envelope encryption separates “many DEKs” from “few well-guarded KEKs”:

```text
KMS/HSM holds KEK
   └─ wraps DEK₁, DEK₂, …
         └─ DEKᵢ AEAD-encrypts objectᵢ
```

### Where keys should live

| Storage | When it fits |
|---------|----------------|
| **Cloud KMS / managed vault** | Default for cloud-hosted apps; IAM (Identity and Access Management)-controlled use without exporting raw KEK |
| **HSM** | High assurance; keys generated and used inside the module boundary |
| **TPM (Trusted Platform Module) / platform keys** | Device-bound keys, measured boot stories |
| **OS keystore / sealed config** | Workstation and some app secrets — still manage rotation |
| **Source repo / AMI / Docker layer** | Never for production private keys or DEKs |

Prefer designs where plaintext key material is **never** readable by ordinary application code for root KEKs — the service calls Encrypt/Decrypt/Sign APIs instead.

## Advanced

### Cryptoperiods and rotation triggers

A **cryptoperiod** is how long a key may be used for its purpose. Rotation triggers include:

| Trigger | Example |
|---------|---------|
| Time | “Signing key replaced yearly” |
| Volume | “DEK replaced after N GCM (Galois/Counter Mode) messages” ([2](./2_Symmetric_AEAD_And_Modes.md)) |
| Personnel | Admin with key access leaves |
| Suspicion | Possible exposure in logs or ticket |
| Algorithm policy | Transition away from deprecated strength ([9](./9_Algorithm_Transition_And_PQC.md)) |

Rotation without **dual-read** (decrypt old version ids) breaks production. Version keys; store key id next to ciphertext.

### Access control on keys is authZ

Treat KMS keys like production datastores:

| Control | Intent |
|---------|--------|
| Separate encrypt vs decrypt roles | Writers cannot necessarily read everything |
| Separate environments | Dev keys cannot decrypt prod |
| Break-glass procedures | Logged, time-bounded |
| Deny by default for CI | Pipelines get short-lived roles, not eternal decrypt |

### FIPS 140-3 — what builders need

FIPS 140-3 specifies **security requirements for cryptographic modules**. Validation means a module was tested against that standard — not that your *application* is automatically secure.

| Phrase | Meaning |
|--------|---------|
| “FIPS 140-3 validated module” | The crypto boundary (HSM, library module build, cloud KMS backend) met tested requirements at a stated level |
| “FIPS mode” in a library | Configuration that restricts algorithms/parameters to approved sets — still need correct use |
| “We are FIPS compliant” (marketing) | Ask: which module certificate? which level? is your deployment inside the validated boundary? |

For high-assurance deployments, keep key use inside hardware-backed modules so plaintext keys are not extractable from app memory (aligned with strong ASVS (Application Security Verification Standard)-style key management expectations).

### Compromise playbook (minimum)

1. Disable or schedule destruction of the affected key version.  
2. Rotate KEKs/DEKs; re-encrypt or invalidate sessions/tokens as needed.  
3. Review logs for decrypt/sign usage during the exposure window.  
4. Treat CI logs, crash dumps, and support bundles as potential key residue.

### Split knowledge and dual control (high assurance)

For root key ceremonies and some HSM operations:

| Control | Intent |
|---------|--------|
| **Split knowledge** | No single person knows the full secret (key shares) |
| **Dual control** | Two authorized people needed to activate a sensitive function |
| **Audit** | Every use of root keys is logged and reviewed |

Most product teams will not run shamir ceremonies weekly — but they **should** prevent one contractor laptop from holding unconstrained prod decrypt.

### Import/export policy

| Policy | Typical default |
|--------|-----------------|
| KEK export | Disabled |
| DEK export | Ephemeral plaintext only inside the encrypting service memory |
| BYOK (bring your own key) / HYOK (hold your own key) | Understand who holds ultimate custody; document trust |
| Backup of key material | Encrypted with a higher KEK; tested restore; access-controlled |

### Mapping to SP 800-57 functions

| Function (concept) | Product translation |
|--------------------|---------------------|
| Generation | KMS CreateKey / GenerateDataKey / HSM generate |
| Distribution | IAM grants + wrap/unwrap; not Slack |
| Storage | Module / vault; encrypted backups of key stores |
| Accountability | CloudTrail/KMS logs; who decrypted what |
| Compromise recovery | Version disable + re-encrypt plan |
| Destruction | Schedule key deletion after cryptoperiod + legal holds |

### Worked failure story (keys, not algorithms)

```text
Service ships with AES-GCM (good) 
  → DEK hardcoded in container image (bad)
    → Image leaked via registry read
      → Attacker decrypts every row despite “strong crypto”
```

The fix is key management ([this note](./5_Key_Management_And_Modules.md)) and secrets hygiene ([7](./7_Cryptographic_Storage_And_Secrets.md)), not a larger AES (Advanced Encryption Standard) key.

### Service identity to keys

Prefer workload identity (cloud role / OIDC (OpenID Connect) to vault) over long-lived static API keys that can decrypt. Static decrypt credentials in CI are a recurring breach pattern — issue short-lived roles for migrate jobs only.

## Apply

1. Draw your envelope diagram: root → KEK → DEK → data; name owners.  
2. Put key ids next to every ciphertext blob and token issuer.  
3. Enforce IAM so only the owning service roles can decrypt prod DEKs.  
4. Define rotation SLOs (time + compromise) and test a dual-read deploy.  
5. Remove private keys from repos/images; scan continuously ([7](./7_Cryptographic_Storage_And_Secrets.md)).

## Self-check

- [ ] Explain DEK vs KEK and why envelope encryption exists  
- [ ] List three rotation triggers beyond “the calendar said so”  
- [ ] State what FIPS 140-3 validation does and does not prove  
- [ ] Know who can decrypt production data in your design  

## Next

- [Password verifiers and key derivation](./6_Password_Verifiers_And_KDF.md)

## Further reading

- [NIST SP 800-57 Part 1 Rev. 5 — Recommendation for Key Management](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final)
- [NIST SP 800-133 Rev. 2 — Cryptographic Key Generation](https://csrc.nist.gov/pubs/sp/800/133/r2/final)
- [FIPS 140-3 — Security Requirements for Cryptographic Modules](https://csrc.nist.gov/pubs/fips/140-3/final)
- [OWASP — Key Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html)
- [OWASP — Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
