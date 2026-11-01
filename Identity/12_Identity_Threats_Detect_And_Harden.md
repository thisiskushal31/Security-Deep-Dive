# Identity threats, detect, and harden

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

Identity programs fail in predictable ways: stolen bearers, phished MFA (multi-factor authentication), consent tricks, IdP (identity provider) admin abuse, and silent federation misvalidation. This note is the **defend** map — mechanisms first, then where detection and IR (incident response) live.

## Concept

### Threat themes (timeless)

| Theme | Example |
|-------|---------|
| **Credential theft** | Password dump, token theft from XSS (cross-site scripting) / malware |
| **Phishing / prompt abuse** | Fake login; MFA fatigue |
| **Federation forgery / injection** | Assertion replay to SP ACS (Assertion Consumer Service) |
| **Client / redirect abuse** | Open redirect; code interception; mix-up |
| **Privilege & consent abuse** | Over-broad OAuth scopes; malicious linked app |
| **Insider / admin path** | IdP admin, help-desk recovery downgrade |
| **NHI (non-human identity) leakage** | Long-lived API keys in repos |

RFC 9700 updates the OAuth threat story; SP 800-63C covers federation transaction threats; classical password/session bugs also sit in [AppSec](../AppSec/README.md).

### Defense layers (stack them)

```text
Phishing-resistant authn ──▶ Correct federation validation ──▶ Bound / short-lived tokens
                                      │
                                      ▼
                         Least privilege + revoke that works
                                      │
                                      ▼
                         Detect misuse ──▶ IR playbooks
```

## Advanced

### Harden (control catalog)

| Control | Points at |
|---------|-----------|
| Kill implicit / password grants; require PKCE (Proof Key for Code Exchange) | [4](./4_OAuth_Authorization.md) |
| Validate `iss` / `aud` / sig / time / nonce | [5](./5_OpenID_Connect.md) · [6](./6_SAML_Enterprise_SSO.md) · [7](./7_Federation_Assurance_And_RP_Validation.md) |
| Prefer phishing-resistant MFA; harden recovery | [3](./3_Authenticators_MFA_And_WebAuthn.md) |
| BFF (Backend for Frontend) / no refresh in JS; DPoP / audience | [8](./8_Tokens_Sessions_And_Client_Patterns.md) |
| Workload identity over static keys | [11](./11_Workload_And_Non_Human_Identity.md) |
| Continuous policy / ZTA PEPs | [9](./9_Zero_Trust_Architecture.md) · [10](./10_Zero_Trust_Maturity_And_Identity_Pillar.md) |

### Detect (signals worth wiring)

| Signal | Why |
|--------|-----|
| Impossible travel / new device + high-risk app | Account takeover |
| MFA fatigue (many denies then accept) | Prompt bombing |
| Refresh token reuse | Theft |
| New OAuth app consent with broad scopes | Consent phishing |
| Assertion validation failures spike | Probe / misconfig / attack |
| IdP admin actions outside change window | Insider / takeover |
| Sudden service account key create | NHI staging |

SIEM / detection engineering / hunt: [Defensive-Ops](../Defensive-Ops/README.md). Purple validation only in authorized labs.

### ITDR (identity threat detection and response)

**ITDR** is the practice of detecting and responding to attacks *on identity systems* (directories, IdPs, privileged paths) — not only endpoint malware. Commercial suites (Microsoft Defender for Identity / Entra ID Protection, CrowdStrike Falcon Identity, Okta threat insights, …) specialize here. Open-source programs usually **compose**: IdP + directory audit logs → SIEM ([Wazuh](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wazuh)) + playbooks in Defensive-Ops.

| ITDR focus | Example signal |
|------------|----------------|
| Credential abuse | Kerberos anomalies, password spray, impossible travel |
| Privilege abuse | Sudden admin role, DCSync-class patterns (lab/authorized AD only) |
| Federation abuse | New SP trust, signing-key change, abnormal consent grants |
| NHI abuse | New API keys, dormant service account reactivation |

Tool pick-list: [16](./16_Identity_Tooling_Map.md). Lifecycle gaps that create ITDR work: [13](./13_Lifecycle_SCIM_And_Directories.md). Privileged paths: [15](./15_Privileged_Access_And_IGA.md).

### Respond (identity-shaped IR)

| Action | Note |
|--------|------|
| Revoke sessions / refresh tokens / API keys | Must be rehearsed — [Defensive-Ops/10](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md) |
| Reset authenticators at comparable AAL (Authentication Assurance Level) | Avoid help-desk downgrade |
| Review OAuth grants / SAML SP trust | Kill malicious clients |
| Rotate IdP signing keys if compromise suspected | Federation-wide impact |
| Preserve logs before wiping | Forensics — Defensive-Ops forensics notes |

### What this folder does *not* replace

| Topic | Home |
|-------|------|
| Injection / broken access in apps | AppSec |
| Packet / endpoint detection depth | Defensive-Ops · Tooling |
| Cloud IAM misconfig matrices | Cloud-Security |
| Password hashing | Cryptography |

## Apply

1. Threat-model one login path (user → IdP → RP → API) with the table above.  
2. Confirm revoke works for SSO session, refresh token, and API key in a lab.  
3. Add or tune three IdP detections (MFA fatigue, refresh reuse, admin anomaly).  
4. Tabletop: stolen OAuth refresh token for a privileged SaaS — owners and clocks.  
5. Schedule a quarterly review of OAuth app consents and SAML SP metadata owners.

## Next

- [Lifecycle, SCIM, and directories](./13_Lifecycle_SCIM_And_Directories.md)

## Further reading

- [RFC 9700 — OAuth 2.0 Security BCP](https://www.rfc-editor.org/rfc/rfc9700)
- [RFC 6819 — OAuth 2.0 Threat Model (historical baseline)](https://www.rfc-editor.org/rfc/rfc6819)
- [NIST SP 800-63C-4 — Federation and Assertions](https://csrc.nist.gov/pubs/sp/800/63/C/4/final)
- [NIST SP 800-63B-4 — Authentication](https://csrc.nist.gov/pubs/sp/800/63/B/4/final)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
