# API security

[← AppSec](./README.md)

APIs are the main product surface for many systems. Secure the **contract**, the **identity**, and the **abuse cases** — not only the framework tutorial.

## Authn and authz at the API

| Concern | Practice |
|---------|----------|
| Authentication | Who is calling (user, app, service)? Prefer standards ([Identity](../Identity/README.md)) |
| Authorization | What may they do on *this* resource? Check object-level authZ (IDOR is common) |
| Transport | TLS everywhere; no “internal HTTP is fine” without a written trust model |
| Tokens | Short-lived; audience/scope bound; store refresh tokens carefully |
| Service-to-service | Workload identity / mTLS — see System-Design Security-Tradeoffs |

## API-specific risks

| Risk | Mitigation grain |
|------|------------------|
| Excessive data exposure | Explicit DTOs; never dump ORM entities |
| Broken object/function authZ | Central policy or consistent middleware; tests |
| Mass assignment | Allowlists on write fields |
| Rate abuse / scraping | Quotas, auth, bot controls at edge |
| SSRF via URL fields | Allowlist schemes/hosts; block link-local metadata |
| Verbose errors | Generic client errors; detail in logs only |

Style and API taxonomy (REST/gRPC/GraphQL): System-Design API notes and Tooling `Specs-Standards/` — do not copy protocol primers here.

## Verify

- Contract tests + authZ cases in CI  
- Authenticated DAST / API scan on staging ([Handbook ZAP](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security/ZAP); Tooling OWASP ZAP install)  
- Threat model when adding a new public route or partner webhook  

## Related home

- Federated identity patterns → [System-Design Security/2](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security/2_Federated_Identity.md)
- Gatekeeper / valet key → System-Design `Security/`
- OAuth stack → Tooling `Specs-Standards/OAuth/`

## Next

- [Identity](../Identity/README.md)
