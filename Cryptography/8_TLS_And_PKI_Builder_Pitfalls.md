# TLS and PKI (Public Key Infrastructure) builder pitfalls

[← Cryptography](./README.md)

> **Progression:** concept → advanced → apply.

How products misuse **TLS (Transport Layer Security)** and certificates: wrong versions, disabled validation, mixed content, and broken trust stores. Certificate model: [3](./3_Asymmetric_Signatures_And_Certificates.md). Handshake and packet labs live in [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive) — this note is **builder/config** depth.

## Concept

### What TLS gives you (when configured correctly)

| Property | How TLS helps |
|----------|----------------|
| Confidentiality on the path | Record protection (AEAD (Authenticated Encryption with Associated Data) in modern suites) |
| Integrity on the path | AEAD / MAC (Message Authentication Code) in the protocol |
| Server authentication | Certificate + chain validation + name check |
| Optional client authentication | mTLS (mutual TLS) with client certs |

TLS does not secure data after it is decrypted on the server, and it does not authorize API actions.

### Protocol versions (product defaults)

| Version | Stance for new systems |
|---------|------------------------|
| **TLS 1.3** | Default preference |
| **TLS 1.2** | Allowed for compatibility with modern cipher suites |
| **TLS 1.0 / 1.1** | Deprecated (RFC 8996); disable |
| **SSL 2.0 / 3.0** | Disable always |

NIST (National Institute of Standards and Technology) SP 800-52 Rev. 2 provides federal-oriented TLS configuration guidance; OWASP (Open Worldwide Application Security Project)’s TLS Cheat Sheet aligns product guidance: prefer 1.3, allow 1.2 carefully, disable legacy.

```text
Client                     Server
  │  ClientHello (1.3/1.2)   │
  │─────────────────────────▶│
  │  Certificate + key share │
  │◀─────────────────────────│
  │  Validate chain + name   │
  │  Derived AEAD keys       │
  │◀════ encrypted app data ═▶│
```

### Certificate validation checklist

| Check | Failure mode if skipped |
|-------|-------------------------|
| Chain to a trust anchor | Accept attacker-issued certs |
| Hostname / identity match | Connect to the wrong host with a valid-looking cert |
| Validity period | Accept expired or not-yet-valid certs |
| Key usage / EKU (Extended Key Usage) | Accept certs for the wrong purpose |
| Revocation policy (as designed) | Accept known-compromised certs longer than necessary |

**Never** ship `verify=False`, custom trust-all managers, or “temporary” validation bypasses in production clients.

### Revocation (CRL / OCSP)

| Mechanism | Role |
|-----------|------|
| **CRL (Certificate Revocation List)** | CA-published list of revoked cert serials |
| **OCSP (Online Certificate Status Protocol)** | Online query for a cert’s status (often stapled by the server) |

| Builder habit | Why |
|---------------|-----|
| Prefer **OCSP stapling** on public HTTPS | Clients get status without a privacy-leaky/extra round trip when stapling works |
| Soft-fail vs hard-fail | Browsers historically soft-failed OCSP; enterprise agents may hard-fail — document your choice |
| Private PKI | You own CRL/OCSP publishing; broken revocation is a standing compromise window |
| Short-lived certs (hours/days) | Shrinks reliance on perfect revocation |

## Advanced

### Cipher suites and forward secrecy

Prefer suites with **ECDHE (Ephemeral Elliptic Curve Diffie–Hellman)** (ephemeral) key agreement so compromising a long-term server private key does not decrypt past recorded sessions (forward secrecy). TLS 1.3 architectures bake modern choices in more tightly than older TLS 1.2 configs — still review 1.2 allow-lists if you must support it.

Avoid legacy RSA (Rivest–Shamir–Adleman) key-transport cipher suites and weak signature/hash combinations required by transition policy ([9](./9_Algorithm_Transition_And_PQC.md)).

### HSTS and mixed content

| Problem | Fix |
|---------|-----|
| Site available on HTTP | Redirect to HTTPS; enable **HSTS (HTTP Strict Transport Security)** when ready |
| HTTPS page loads active HTTP scripts | Mixed content — browsers block or warn; fix asset URLs |
| Cookie without Secure | Session theft on cleartext requests |

HSTS preload is a deliberate step after you know HTTPS works everywhere — do not flip it casually.

### mTLS pitfalls

| Pitfall | Outcome |
|---------|---------|
| Client certs without revocation story | Stolen laptop stays trusted |
| Same client cert on many devices | Impossible accountability |
| Terminating mTLS at a proxy and not binding identity upstream | Backend trusts a header anyone can forge |

Propagate authenticated identity explicitly after termination (SPIFFE/SVID-style or signed internal tokens) — do not invent a forgeable `X-Client-DN` header culture.

### Private PKI vs public CA

| Choice | Fits |
|--------|------|
| Public CA (Let’s Encrypt, commercial) | Public websites, many SaaS endpoints |
| Private enterprise CA | Internal mTLS, service mesh identities |
| Self-signed without distribution of trust anchors | Labs only |

Pinning and private CA distribution are operational programs — treat trust-anchor updates like key rotation.

### Common library foot-guns

| Foot-gun | Safer habit |
|----------|-------------|
| Custom `HostnameVerifier` that returns true | Delete it |
| Ignoring intermediate certs / sending wrong chain | Serve full chain |
| Expired cert monitoring absent | Alert on days-to-expiry |
| Wildcard certs on shared hosting | Minimize blast radius; prefer specific names |

### Certificate Transparency and pinning

| Control | Builder takeaway |
|---------|------------------|
| **Certificate Transparency (CT (Certificate Transparency))** | Public TLS certs are logged; browsers enforce CT for many public certs — operators monitor for unexpected issuances of their domains |
| **Pinning (SPKI (Subject Public Key Info) / cert)** | Can harden mobile/high-assurance clients against rogue CAs; catastrophic if pins ship wrong or fail to rotate — prefer backup pins and short pin lifetimes |
| **CAA (Certification Authority Authorization) DNS records** | Limit which public CAs may issue for your domain |

Most browser apps should rely on the platform trust store + CT rather than custom pinning. Mobile and high-assurance agents may pin with a tested rotation story.

### Automation (ACME (Automatic Certificate Management Environment)) pitfalls

Automated issuance (ACME / Let’s Encrypt-class flows) removes expiry outages when healthy — and creates new failure modes:

| Risk | Habit |
|------|-------|
| Broken HTTP-01 / DNS-01 validation | Monitor renewal job success, not only cert expiry |
| Hijacked DNS → fraudulent cert | Protect DNS admin paths; consider CAA |
| Central ACME account key theft | Store account keys like production secrets ([7](./7_Cryptographic_Storage_And_Secrets.md)) |

### Downgrade and opportunistic cleartext

| Anti-pattern | Fix |
|--------------|-----|
| Soft-fail TLS (fall back to cleartext on error) | Fail closed for security-sensitive channels |
| Allowlisting ancient cipher suites “for one partner” | Time-box exceptions; terminate legacy at a proxy |
| Disabling SNI (Server Name Indication) or name checks for “internal IP certs” | Use proper internal names / private CA |

## Apply

1. Disable TLS 1.0/1.1 (and SSL) on all terminators; prefer TLS 1.3.  
2. Scan code for validation bypasses; block them in CI.  
3. Turn on HSTS for public sites once HTTPS is universal.  
4. Monitor certificate expiry and chain completeness.  
5. For service identity, prefer managed mTLS / workload identity over long-lived shared client certs.

## Self-check

- [ ] State default version policy (1.3 / 1.2 / legacy)  
- [ ] List mandatory certificate checks  
- [ ] Explain why `verify=False` is a product vulnerability  
- [ ] Know where handshake deep-dives live (Networks) vs this note  

## Next

- [Algorithm transition and post-quantum readiness](./9_Algorithm_Transition_And_PQC.md)

## Further reading

- [NIST SP 800-52 Rev. 2 — Guidelines for TLS Implementations](https://csrc.nist.gov/pubs/sp/800/52/r2/final)
- [OWASP — Transport Layer Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Security_Cheat_Sheet.html)
- [IETF RFC 8446 — TLS 1.3](https://www.rfc-editor.org/rfc/rfc8446.html)
- [IETF RFC 8996 — Deprecating TLS 1.0 and 1.1](https://www.rfc-editor.org/rfc/rfc8996.html)
- [NCSC — Using TLS to protect data](https://www.ncsc.gov.uk/guidance/using-tls-to-protect-data)
- [Certificate Transparency](https://certificate.transparency.dev/)
- [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive)
