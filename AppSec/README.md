# Application security (practice deep dive)

[← README](../README.md)

Build and verify secure web applications and APIs: secure SDLC, OWASP Top 10:2025, access control, injection/XSS, API Top 10:2023, ASVS verification, and proactive controls. Pipeline gate *enforcement* lives in [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security). Network/wire depth lives in [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive).

Each note follows **concept → advanced → apply**. Official Further reading only. Lab/adversarial work stays **authorized and isolated** — [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

```text
1 Secure SDLC / AppSec program → 2 OWASP Top 10:2025 in practice
  → 3 Access control & authentication → 4 Injection & output encoding
    → 5 API Security Top 10:2023 → 6 ASVS verification practice
      → 7 Proactive controls & testing literacy
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [Secure SDLC and AppSec program](./1_Secure_SDLC_And_AppSec_Program.md) |
| 2 | [OWASP Top 10:2025 in practice](./2_OWASP_Top_10_2025_In_Practice.md) |
| 3 | [Access control and authentication](./3_Access_Control_And_Authentication.md) |
| 4 | [Injection and output encoding](./4_Injection_And_Output_Encoding.md) |
| 5 | [API Security Top 10:2023](./5_API_Security_Top_10_2023.md) |
| 6 | [ASVS verification practice](./6_ASVS_Verification_Practice.md) |
| 7 | [Proactive controls and testing literacy](./7_Proactive_Controls_And_Testing_Literacy.md) |

Learning path **1 → 7** complete for this folder.

## Learning path

`1 → 2 → 3 → 4 → 5 → 6 → 7`

## Topic map

| Area | Home |
|------|------|
| SSDF / SAMM / Secure by Design; risk path; gates ownership | 1 |
| Top 10:2025 catalog, 2021→2025 shifts, how failures happen, control owners | 2 |
| Broken access control, authn, sessions, CSRF/SSRF as access-class | 3 |
| Injection, XSS, XXE, uploads, deserialize, headers/CSP, open redirects | 4 |
| BOLA / BFLA / BOPLA, business-flow abuse, API inventory | 5 |
| ASVS 5.0 levels and chapter map; cite `v5.0.0-x.y.z` | 6 |
| Proactive Controls C1–C10; headers/paved roads; WSTG literacy; CI handoff | 7 |

## Related homes (do not duplicate)

| Topic | Home |
|-------|------|
| Threat modeling workshops | [Threat-Modeling](../Threat-Modeling/README.md) |
| Identity / OAuth / ZT | [Identity](../Identity/README.md) |
| Crypto pitfalls | [Cryptography](../Cryptography/README.md) |
| Cloud resources, posture, runtimes, supply chain, cloud IR | [Cloud-Security](../Cloud-Security/README.md) |
| Vuln prioritization | [Vulnerability-Management](../Vulnerability-Management/README.md) |
| AI output → classic sinks | [AI-Security](../AI-Security/README.md) (LLM10) |
| CI gate chain / AST tools | [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security) |
| PTES / lab exploitation | [Offensive](../Offensive/README.md) |
