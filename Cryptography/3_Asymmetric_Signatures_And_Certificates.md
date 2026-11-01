# Asymmetric crypto, signatures, and certificates

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

Public-key cryptography for builders: key pairs, encryption vs signatures, hybrid designs with symmetric crypto, and certificates as **bindings** — not magic identity. Symmetric bulk crypto: [2](./2_Symmetric_AEAD_And_Modes.md). TLS (Transport Layer Security) validation pitfalls: [8](./8_TLS_And_PKI_Builder_Pitfalls.md). PQC (post-quantum cryptography) signature/KEM (key-encapsulation mechanism) standards: [9](./9_Algorithm_Transition_And_PQC.md).

## Concept

### Key pairs

Asymmetric (public-key) algorithms use a **key pair**: a public key you can distribute, and a private key you must protect.

| Operation | Uses | Provides |
|-----------|------|----------|
| Encrypt to a recipient | Recipient’s **public** key | Confidentiality for small payloads / key wrapping (algorithm-dependent) |
| Decrypt | Recipient’s **private** key | Recovery of that ciphertext |
| Sign | Signer’s **private** key | Integrity + origin evidence under key custody |
| Verify | Signer’s **public** key | Check that signature matches data + claimed key |

Never invent “encrypt with private key” as a substitute for signatures. Use the library’s **sign/verify** APIs.

### Why products still use symmetric crypto for bulk data

Public-key operations are typically slower and constrained. Modern designs are **hybrid**:

```text
1. Generate ephemeral DEK (symmetric)
2. AEAD-encrypt payload with DEK
3. Wrap / encapsulate DEK to recipient public key (or via KMS)
4. Store/send: wrapped DEK + AEAD ciphertext + IV + tag
```

TLS does a version of this on the wire (handshake establishes symmetric keys; records use AEAD (Authenticated Encryption with Associated Data)). Application envelope encryption with a KMS (Key Management Service) does the same pattern in storage ([7](./7_Cryptographic_Storage_And_Secrets.md)).

### Signatures vs MACs (Message Authentication Codes)

| | Digital signature | MAC |
|--|-------------------|-----|
| Keys | Asymmetric pair | Shared secret |
| Who can verify | Anyone with the public key | Anyone with the shared secret (same set that can forge) |
| Non-repudiation story | Stronger when private key is well-custodied | Weak — holders of the secret can forge |
| Typical use | Code signing, documents, certs, tokens with public verification | API request auth between two services that share a secret |

If both sides already share a secret and only need integrity between them, a MAC (or AEAD) may be enough. If many verifiers must check one producer without holding a forging key, use signatures.

### Key agreement (Diffie–Hellman / ECDH)

**Key agreement** lets two parties compute a shared secret without sending that secret on the wire. Classic **Diffie–Hellman (DH)** and **ECDH (Elliptic Curve Diffie–Hellman)** (including X25519-class curves in modern stacks) are the usual builders’ tools — almost always via TLS or a vetted library, not a home-grown handshake.

```text
Alice ephemeral key pair          Bob ephemeral key pair
        │                                  │
        └──── public values exchanged ─────┘
                    │
           shared secret (IKM)
                    │
              HKDF → traffic keys
```

| Idea | Builder meaning |
|------|-----------------|
| **Ephemeral** agreement | Fresh key share per session → **forward secrecy** (stolen long-term key should not decrypt past sessions) |
| **Static** keys only | Weaker forward-secrecy story; avoid for new interactive protocols |
| **Authenticated** agreement | Combine agreement with signatures/certs so you are not agreeing with an impostor (TLS does this) |

Here **IKM** is input keying material for **HKDF (HMAC-based Key Derivation Function)** ([10](./10_Hashing_MACs_And_Key_Derivation.md)).

Product rule: prefer TLS 1.3 (or a maintained Noise/Signal-class protocol) over inventing DH+HMAC yourself. ASVS (Application Security Verification Standard) expects approved key-exchange parameters when you *do* use public-key agreement outside TLS.

### Certificates bind keys to names

A **certificate** is a signed statement: “public key \(PK\) belongs to name \(N\)” (plus validity, usage constraints, issuer). A **PKI (Public Key Infrastructure)** is the set of policies, CAs (Certificate Authorities), and validation rules that make that statement trustworthy.

```text
Root / trust anchor
   └─ signs Intermediate CA cert
         └─ signs Server/Leaf cert (binds domain or identity → public key)
```

| Term | Builder meaning |
|------|-----------------|
| **Trust anchor** | Root key you explicitly trust (browser/OS store or your enterprise pin) |
| **Chain** | Leaf → intermediate(s) → anchor |
| **SAN (Subject Alternative Name) / subject** | Names the cert is valid for (domains, etc.) |
| **Revocation** | CRL (Certificate Revocation List) / OCSP (Online Certificate Status Protocol) — “do not trust this cert anymore” |
| **Self-signed** | Useful in closed labs; dangerous if clients disable validation to make it “work” |

Certificates do not replace application authZ. They help answer “am I talking to the key that the CA (Certificate Authority) vouched for under this name?”

## Advanced

### Algorithm families you will see (classical)

| Family | Common product roles | Notes for builders |
|--------|----------------------|--------------------|
| **RSA (Rivest–Shamir–Adleman)** | Signatures; legacy encryption / key transport | Prefer library OAEP for encryption; prefer PSS (Probabilistic Signature Scheme) for signatures where supported; watch key size transitions ([9](./9_Algorithm_Transition_And_PQC.md)) |
| **ECDSA (Elliptic Curve Digital Signature Algorithm) / EdDSA (Edwards-curve Digital Signature Algorithm)** | Signatures on curves | Smaller keys/signatures; use named curves your stack supports |
| **ECDH (Elliptic Curve Diffie–Hellman) / X25519** | Key agreement | Establishes shared secrets; not a signature |

Federal digital signature standards live in FIPS 186-5 for classical schemes; post-quantum signatures are separate FIPS (204/205) covered in [9](./9_Algorithm_Transition_And_PQC.md).

### Padding and API traps

| Trap | Safer habit |
|------|-------------|
| Raw RSA without OAEP | Use maintained **RSAES-OAEP** implementations |
| Homemade “sign by encrypting hash with private key” | Use **Sign** API with approved hash + padding (PSS/PKCS where required) |
| Truncating hashes or mixing MD5/SHA-1 for new signatures | Follow current transition guidance; SHA-1 is legacy |
| Reusing one key for encrypt and sign | Separate keys by purpose (and certificate key usage) |

### JWT (JSON Web Token) / token pointer

JSON Web Tokens use HMAC (`HS*`) or asymmetric signatures (`RS*`, `ES*`) — and optionally encryption (JWE (JSON Web Encryption)). Crypto foot-guns (`alg=none`, key confusion, forever keys, signed≠encrypted) and webhook HMAC patterns: **[11](./11_Applied_Patterns_Tokens_And_Signing.md)**. AuthZ/session product depth: [AppSec/3](../AppSec/3_Access_Control_And_Authentication.md).

### Trust decisions beat algorithm brand names

A 4096-bit RSA cert is worthless if your client:

1. Does not validate the chain to a trust anchor,  
2. Does not check the name,  
3. Ignores validity dates, or  
4. Pins nothing and accepts a substitute CA in a hostile network.

That failure mode is explored as product bugs in [8](./8_TLS_And_PKI_Builder_Pitfalls.md).

## Apply

1. Inventory every private key: purpose (sign vs decrypt vs agree), owner, storage, rotation.  
2. For each “we encrypt with RSA” feature, confirm OAEP (or KMS wrap) and prefer hybrid AEAD for payloads.  
3. Separate signing and encryption keys; encode purpose in procedure and in cert key usage.  
4. Document trust anchors for every TLS/mTLS (mutual Transport Layer Security) client you ship (OS store vs explicit pin).  
5. Ban `verify=False` / custom trust-all callbacks in production code paths.

## Self-check

- [ ] Diagram hybrid encryption (DEK (Data Encryption Key) + wrap + AEAD)  
- [ ] Contrast signature and MAC who-can-forge  
- [ ] Explain what a certificate asserts (and what it does not)  
- [ ] Name two JWT crypto foot-guns  

## Next

- [Randomness and key generation](./4_Randomness_And_Key_Generation.md)

## Further reading

- [FIPS 186-5 — Digital Signature Standard (DSS)](https://csrc.nist.gov/pubs/fips/186/5/final)
- [NIST SP 800-175B Rev. 1 — Cryptographic Mechanisms](https://csrc.nist.gov/pubs/sp/800/175/b/r1/final)
- [OWASP — Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [OWASP — Key Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html)
