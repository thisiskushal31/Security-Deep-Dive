# Algorithm transition and post-quantum readiness

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

Cryptographic algorithms **age**. This note is how programs retire weak algorithms and how to prepare for **PQC (post-quantum cryptography)** using NIST's standardized KEMs and signatures — without turning the team into lattice researchers. Classical primitives: [2](./2_Symmetric_AEAD_And_Modes.md) · [3](./3_Asymmetric_Signatures_And_Certificates.md). Key lifecycle: [5](./5_Key_Management_And_Modules.md).

## Concept

### Transition is a program, not a blog post

NIST SP 800-131A provides guidance on transitioning cryptographic algorithms and key lengths (Rev. 2 is the current final at collection time; watch CSRC for newer revisions). Pair it with a **cryptographic inventory** ([1](./1_Goals_Threat_Model_And_Misuse.md)) so migration tickets have owners. Product implications:

| Habit | Why |
|-------|-----|
| Inventory algorithms in use | You cannot migrate what you cannot see |
| Separate “deprecated for new use” vs “must remove now” | Avoid big-bang breakage |
| Dual-verify / dual-encrypt windows | Roll forward safely |
| Tie transitions to key versions | Ciphertext and tokens carry algorithm/key ids |

```text
Discover → Policy (new use banned) → Dual support → Primary switch → Remove legacy
```

### What usually needs a schedule

| Area | Examples of debt |
|------|------------------|
| Hash / signature | SHA-1 signatures; MD5 anywhere security-critical |
| RSA (Rivest–Shamir–Adleman) / DH sizes | Short moduli still hanging in old PKI (Public Key Infrastructure) or protocols |
| TLS (Transport Layer Security) | Legacy versions and weak suites ([8](./8_TLS_And_PKI_Builder_Pitfalls.md)) |
| Password hashing | Unsalted SHA-*; low work factors ([6](./6_Password_Verifiers_And_KDF.md)) |
| Symmetric | Non-AEAD (Authenticated Encryption with Associated Data) modes for new data ([2](./2_Symmetric_AEAD_And_Modes.md)) |

### Security strength (128-bit floor)

ASVS (Application Security Verification Standard)-style guidance: aim for at least **128 bits of security strength** from the algorithm + key size + configuration together. Rough builder intuition (not a substitute for current NIST tables):

| Family | Ballpark for ~128-bit strength |
|--------|--------------------------------|
| AES | AES-128 or AES-256 in approved modes |
| ECC (elliptic-curve cryptography) | ~256-bit curves (for example P-256 / X25519-class) |
| RSA | On the order of **3072-bit** moduli (2048-bit is legacy/transition territory) |

Symmetric AES-128/256 remains widely appropriate when used in AEAD; transition pressure today is often stronger on **public-key** algorithms and on protocol versions.

### Why “harvest now, decrypt later” matters

Attackers can record TLS or stored asymmetric-wrapped ciphertext today and decrypt later if large-scale quantum attacks on classical public-key algorithms become practical. Long-lived confidentiality requirements (archives, identities, VPN/session keys with long privacy needs) drive earlier PQC migration than short-lived public data.

## Advanced

### NIST PQC standards (builder names)

NIST standardized post-quantum algorithms as FIPS (Federal Information Processing Standard) in 2024:

| Standard | Algorithm name | Role |
|----------|----------------|------|
| **FIPS 203** | **ML-KEM (Module-Lattice-Based Key-Encapsulation Mechanism)** (Module-Lattice-Based Key-Encapsulation Mechanism) | Key establishment / encapsulation |
| **FIPS 204** | **ML-DSA (Module-Lattice-Based Digital Signature Algorithm)** (Module-Lattice-Based Digital Signature Algorithm) | Signatures |
| **FIPS 205** | **SLH-DSA (Stateless Hash-Based Digital Signature Algorithm)** (Stateless Hash-Based Digital Signature Algorithm) | Hash-based signatures |

You do not need the math to plan: treat these as **new algorithm identifiers** your libraries, TLS stacks, PKI, and protocols must eventually speak.

```text
Classical today          Transition designs              Target
ECDH / RSA-KEM    →    hybrid KEM (classical + ML-KEM) → ML-KEM class
ECDSA / RSA sign  →    hybrid or dual signatures         → ML-DSA / SLH-DSA
```

**Hybrid** designs combine classical and PQC so a break of one family does not immediately break confidentiality/authenticity during the migration era. Prefer vendor/stack hybrids that are standardized or widely reviewed — do not invent a hybrid combinator in app code.

### Program checklist for PQC readiness

| Step | Outcome |
|------|---------|
| 1. Inventory public-key use | TLS, SSH, code signing, document signing, tokens, S/MIME, VPN, disk escrow |
| 2. Rank by confidentiality lifetime | Archives and roots first |
| 3. Track library/OS readiness | OpenSSL/BoringSSL, runtimes, HSMs, cloud KMS (Key Management Service) roadmaps |
| 4. Pilot hybrid TLS / signing in non-prod | Measure size, CPU, fail modes |
| 5. Update procurement language | Require PQC roadmaps from vendors |
| 6. Plan cert/profile changes | Larger keys/signatures → packet MTU (Maximum Transmission Unit), smart cards, HSMs |

CISA’s quantum readiness materials frame operational planning for agencies and enterprises — use them for program language, not as a substitute for NIST algorithm specs.

### CNSA (Commercial National Security Algorithm) / sector profiles

National security and some regulated sectors publish algorithm suites (for example CNSA 2.0 guidance from NSA). If you are in scope, follow that profile’s timelines; if not, still watch them as leading indicators of ecosystem direction. Fetch the current PDF from official defense/NSA channels when required (some CDNs block automated download).

### What not to do

| Anti-pattern | Why |
|--------------|-----|
| Home-grown PQC implementation | Compatibility and safety risk — wait for maintained libraries |
| “AES-256 is quantum-proof so we’re done” | Symmetric key sizes are only part of the story; public-key breaks dominate the scare |
| Big-bang cutover without dual support | Outage + rollback chaos |
| Ignoring HSMs and smart cards | Hardware may lag software stacks |

### Size and performance realities (plan for them)

PQC public keys and signatures are often **larger** than classical ECDSA (Elliptic Curve Digital Signature Algorithm). That affects:

| Surface | Impact |
|---------|--------|
| TLS handshake | More bytes; more CPU on terminate farms |
| PKI / smart cards | May not fit old token profiles |
| Code signing / firmware | Image size and verify time |
| HSMs | Firmware support lag |

Pilot early on your highest-traffic TLS terminators and your constrained signing devices.

### Governance hooks

| Hook | Action |
|------|--------|
| SSDF / secure development | Approved algorithms list in eng standards |
| Procurement | PQC roadmap clauses for VPN, PKI, KMS vendors |
| GRC evidence | Inventory + transition tickets as control operation proof |
| Threat intel | Watch NIST/CISA updates; do not chase random “quantum safe” marketing |

## Apply

1. Produce an algorithm inventory (protocols, libraries, key sizes, owners).  
2. Ban new SHA-1 / MD5 / TLS below 1.2 in policy and CI where detectable.  
3. Assign a PQC pilot owner; track FIPS 203/204/205 support in your TLS and KMS vendors quarterly.  
4. For data that must stay confidential more than 10 years, escalate hybrid/PQC planning now.  
5. Link algorithm removal to key version deprecation in your KMS runbooks ([5](./5_Key_Management_And_Modules.md)).

## Self-check

- [ ] Explain 800-131A’s role as transition guidance  
- [ ] Name ML-KEM, ML-DSA, and SLH-DSA with their FIPS numbers  
- [ ] Define hybrid KEM in one sentence  
- [ ] List three inventory places public-key crypto hides in your stack  

## Next

- Back to [Cryptography README](./README.md) · revisit [1](./1_Goals_Threat_Model_And_Misuse.md) if onboarding someone new

## Further reading

- [NIST SP 800-131A Rev. 2 — Transitioning Algorithms and Key Lengths](https://csrc.nist.gov/pubs/sp/800/131/a/r2/final)
- [NIST Post-Quantum Cryptography project](https://csrc.nist.gov/Projects/post-quantum-cryptography)
- [FIPS 203 — ML-KEM](https://csrc.nist.gov/pubs/fips/203/final)
- [FIPS 204 — ML-DSA](https://csrc.nist.gov/pubs/fips/204/final)
- [FIPS 205 — SLH-DSA](https://csrc.nist.gov/pubs/fips/205/final)
- [CISA — Quantum](https://www.cisa.gov/quantum)
- [NIST Cryptographic Standards and Guidelines](https://csrc.nist.gov/Projects/cryptographic-standards-and-guidelines)
