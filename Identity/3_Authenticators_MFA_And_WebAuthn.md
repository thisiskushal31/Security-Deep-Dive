# Authenticators, MFA, and WebAuthn

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**Authentication** establishes that a claimant controls authenticators bound to a subscriber. NIST SP 800-63B-4 defines AAL (Authentication Assurance Level) requirements and authenticator properties. MFA (multi-factor authentication) is necessary but not sufficient — **phishing resistance** is the bar modern programs miss.

## Concept

### Factors (still the vocabulary)

| Factor | Examples |
|--------|----------|
| Something you **know** | Password, PIN |
| Something you **have** | OTP (one-time password) device, smart card, phone-bound passkey |
| Something you **are** | Biometric unlock *local to* an authenticator (the RP (relying party) usually verifies a key, not a raw biometric) |

MFA means at least two different factor *types* in the authentication event — two passwords is not MFA.

### AAL as a program dial

| AAL grain | Intent |
|-----------|--------|
| **AAL1** | Some confidence; single-factor allowed in baseline cases |
| **AAL2** | MFA; hardened against common replay / theft paths per 63B |
| **AAL3** | Hardened MFA with verifier-impersonation / phishing resistance properties required by 63B |

Exact authenticator combinations are normative in 63B — do not invent “AAL3 because we bought a vendor pack.”

### Phishing-resistant vs “we have MFA”

| Authenticator class | Phishing reality |
|---------------------|------------------|
| SMS / voice OTP | Interceptable; users type codes into fake sites |
| OTP apps (TOTP) | Better than SMS; still typed into phishing pages |
| Push “approve?” | MFA fatigue / prompt bombing |
| **WebAuthn / FIDO passkeys** (public-key, origin-bound) | Challenge signed only for the real RP origin — phishing-resistant when deployed correctly |
| PIV / smart card (where used) | Classic phishing-resistant path for many enterprises |

OMB M-22-09 pushes federal programs toward **phishing-resistant** MFA for staff — the same pressure shows up in private-sector Zero Trust programs: [10](./10_Zero_Trust_Maturity_And_Identity_Pillar.md).

## Advanced

### WebAuthn / FIDO2 in one diagram

```text
RP (origin) ──challenge──▶ Browser / platform ──CTAP──▶ Authenticator
                              │
                              ▼
                    Public-key credential
                    (scoped to RP ID / origin)
                              │
                              ▼
RP verifies signature with stored public key (+ attestation policy if used)
```

| Piece | Role |
|-------|------|
| **WebAuthn** | W3C API between site and authenticator via the user agent |
| **CTAP (Client to Authenticator Protocol)** | How platforms talk to external authenticators (USB/NFC/BLE) |
| **Passkey** | Discoverable / synced or device-bound credentials marketed for password replacement |
| **Attestation** | Optional proof of authenticator model — privacy vs enterprise inventory trade-off |

### Synced passkeys vs device-bound (decide on purpose)

| Flavor | Upside | Program caution |
|--------|--------|-----------------|
| **Synced** (platform cloud keychain) | UX; recover across user devices | Cloud account takeover may widen authenticator reach — treat platform account AAL seriously |
| **Device-bound** / hardware-only | Stronger phishing + theft story | Loss/recovery process must be designed, not improvised |
| **Hybrid enterprise** | Conditional access + managed devices | Prefer managed attestation / inventory where compliance needs device proof |

Workforce Zero Trust programs (M-22-09 style) care that MFA is **phishing-resistant**, not merely “a second factor exists.” Customer CIAM may accept synced passkeys for conversion — document the threat trade-off.

### Lifecycle events that break programs

| Event | Failure if ignored |
|-------|--------------------|
| Loss / theft | Account recovery weaker than original AAL |
| Subscriber shares OTP | Policy and detection, not only tech |
| Authenticator cloning (where possible) | Prefer authenticators with restricted cloning properties at higher AAL |
| Stale sessions after step-up | Re-auth on risk; revoke refresh paths — [8](./8_Tokens_Sessions_And_Client_Patterns.md) |

### Verifier duties (RP or IdP)

| Check | Why |
|-------|-----|
| Authenticator allowed for claimed AAL | Inventory beat marketing |
| Rate-limit / lockout without easy DoS (denial of service) of victims | Classic authn ops |
| Secure recovery comparable to AAL | Help desk is the side door |
| Logging of authn success/fail/MFA fatigue | Feeds [Defensive-Ops](../Defensive-Ops/README.md) |

Password storage algorithms belong in [Cryptography](../Cryptography/README.md) — here the issue is *whether* passwords exist at your target AAL at all.

## Apply

1. Inventory authenticators in production (password, SMS, TOTP, push, WebAuthn, hardware).  
2. Mark each phishing-resistant **yes/no** for your threat model.  
3. Map workforce vs customer vs privileged to target AALs.  
4. Write the lost-device recovery path; compare its strength to login.  
5. Pilot passkeys on one RP; confirm origin binding and backup/sync policy intentionally.

## Next

- [OAuth authorization](./4_OAuth_Authorization.md)

## Further reading

- [NIST SP 800-63B-4 — Authentication and Authenticator Management](https://csrc.nist.gov/pubs/sp/800/63/B/4/final)
- [W3C Web Authentication (WebAuthn) Level 3](https://www.w3.org/TR/webauthn-3/)
- [FIDO Alliance — Specifications](https://fidoalliance.org/specifications/)
- [OMB M-22-09 — Federal Zero Trust Strategy](https://www.whitehouse.gov/wp-content/uploads/2022/01/M-22-09.pdf)
