# OWASP and secure SDLC

[← AppSec](./README.md)

Application security as a **program**: risks you design against, and when in the lifecycle you catch them. Do not duplicate the CI gate chain here.

## OWASP Top 10 as a checklist

Use the current [OWASP Top 10](https://owasp.org/www-project-top-ten/) as a **risk reminder**, not a syllabus to memorize by number:

| Theme (examples) | Program response |
|------------------|------------------|
| Broken access control | Explicit authZ checks; deny by default; test IDOR |
| Injection / unsafe handling | Parameterized queries; encode output; SAST |
| Auth failures | MFA, secure session, federated IdP |
| Cryptographic failures | Managed crypto; TLS; no homemade ciphers ([Cryptography](../Cryptography/README.md)) |
| Misconfig / vulnerable components | Hardening + SCA + patch SLA |
| SSRF / design flaws | Egress control; threat model new trust boundaries |

Revisit Top 10 when the published list updates; the **process** (design → build → verify → operate) matters more than the year label.

## Secure SDLC (SAMM-shaped)

Align with [OWASP SAMM](https://owaspsamm.org/model/) functions without rebuilding SAMM here:

| Phase | AppSec habit |
|-------|--------------|
| Design | Threat model ([Foundations/2](../Foundations/2_STRIDE_And_Threat_Modeling.md)); data class |
| Implementation | Secure coding standards; dependency hygiene |
| Verification | Code review + SAST/DAST/SCA in CI |
| Operations | Patch, WAF tune, incident learnings back into design |

## Delivery gates live in Handbook

Ordered PR→prod controls and AST method names:

- [Security gate chain](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/4_Security_Gate_Chain.md)
- [AST methods and tools](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/6_AST_Methods_And_Tools.md)

This note owns **why** AppSec exists in the SDLC; Handbook owns **how** the pipeline enforces it.

## Related home (do not rewrite here)

- Design trade-offs → System-Design `Security-Tradeoffs/`
- OAuth how-to → Tooling `Specs-Standards/OAuth/`

## Next

- [API security](./2_API_Security.md)
