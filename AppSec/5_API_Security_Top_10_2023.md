# API Security Top 10:2023

[← AppSec (application security)](./README.md)

> **Progression:** concept → advanced → apply.

How the **OWASP API Security Top 10:2023** differs from the web Top 10 — and how to design, test, and inventory APIs so BOLA (Broken Object Level Authorization)/BFLA (Broken Function Level Authorization)/BOPLA (Broken Object Property Level Authorization) and business-flow abuse do not become the default failure mode. Web access control: [3](./3_Access_Control_And_Authentication.md). Injection/XSS (Cross-Site Scripting): [4](./4_Injection_And_Output_Encoding.md). Requirements checklist: [6](./6_ASVS_Verification_Practice.md) (esp. V4).

APIs are first-class attack surfaces: clients send object IDs, property bags, and batch operations; servers often stay mostly stateless. The API Top 10 is an **awareness** document for people who build and run APIs — complementary to (not a replacement for) the web Top 10.

```text
Client (mobile / SPA / partner / script)
  → Authn (who) → AuthZ object + property + function
  → Business-flow & resource limits
  → Trust boundary for third-party responses
```

## Concept

### The 2023 list (builder first response)

| ID | Risk | Builder first response |
|----|------|------------------------|
| **API1** | Broken Object Level Authorization (BOLA) | AuthZ check on **every** client-supplied object id |
| **API2** | Broken Authentication | Protect all authn + recovery flows; validate tokens; MFA (multi-factor authentication) |
| **API3** | Broken Object Property Level Authorization (BOPLA) | Cherry-pick read/write fields; no mass assign / over-expose |
| **API4** | Unrestricted Resource Consumption | Rate limits, payload/size caps, spend limits on paid APIs |
| **API5** | Broken Function Level Authorization (BFLA) | Deny-by-default function authZ; admin ≠ “guessed URL” |
| **API6** | Unrestricted Access to Sensitive Business Flows | Anti-automation on business-harmful flows (scalping, spam) |
| **API7** | Server-Side Request Forgery | Validate/allowlist URIs the API fetches ([3](./3_Access_Control_And_Authentication.md)) |
| **API8** | Security Misconfiguration | Harden stacks; least-open CORS/headers/debug; config as code |
| **API9** | Improper Inventory Management | Host + version + data-flow inventory; retire old APIs |
| **API10** | Unsafe Consumption of APIs | Treat third-party responses as untrusted input |

### 2019 → 2023 shifts worth remembering

| Change | Meaning |
|--------|---------|
| **API3 BOPLA** | Merges 2019 Excessive Data Exposure + Mass Assignment under one root cause: missing **property-level** authZ |
| **API4 Resource Consumption** | Broader than “rate limit missing” — includes paid SMS/email burn, GraphQL batching, unbounded queries |
| **API6 Business Flows** | New: correct authZ can still wreck the business if automation is free |
| **API10 Unsafe Consumption** | New: your trust in partner APIs becomes the sink |
| Injection / logging as standalone API ranks | Still real — map to web Top 10 A05/A09 and ASVS; API list re-prioritized |

```text
2019 Excessive Data Exposure + Mass Assignment ──▶ 2023 API3 BOPLA
2019 Lack of Resources & Rate Limiting ──────────▶ 2023 API4 (expanded)
2023 API6 Business Flows ◀── new
2023 API10 Unsafe Consumption ◀── new
```

### BOLA vs BFLA vs BOPLA

| Risk | Question the check answers | Typical miss |
|------|----------------------------|--------------|
| **BOLA** | May *this* user act on *this object*? | Swap `shopName` / VIN / document id |
| **BFLA** | May *this* user call *this function* at all? | `POST /api/invites/new` as a normal user |
| **BOPLA** | May they *read/write these fields*? | Response leaks `recentLocation`; body sets `role=admin` |

In BOLA the endpoint is **supposed** to be reachable; the bug is the object. In BFLA the endpoint/function should have been denied. Comparing JWT (JSON Web Token) `sub` to a path user id fixes only a **small subset** of BOLA cases.

## Advanced

### API1 — BOLA

Servers lean on client-sent IDs (path, query, header, body — sequential ints, UUIDs, strings). Response status/body usually reveals success. Impact: disclosure, tampering, destruction; sometimes account takeover.

**Scenario anatomies (authorized education):**

1. Revenue charts at `/shops/{shopName}/revenue_data.json` + shop-name listing → script walks every shop.  
2. Vehicle remote API accepts VIN without ownership → control others’ cars.  
3. GraphQL `deleteReports(reportKeys:…)` deletes without permission check.

**Break it:**

| Control | Practice |
|---------|----------|
| Policy + hierarchy aware authZ | On **every** function that uses client input to load a record |
| Prefer unpredictable IDs | Helps; **never** replaces ownership checks |
| Automated authZ tests | Fail the build on regressions |

### API2 — Broken authentication

Authn and **forgot/reset password** are equally sensitive. Vulnerable shapes: stuffing/brute without lockout/CAPTCHA; weak passwords; tokens/passwords in URLs; sensitive account changes without re-auth; unsigned/`alg:none`/unexpired JWTs; weak password storage; microservice-to-microservice with no/weak auth; GraphQL **batching** that bypasses per-request rate limits on login.

**Break it:** inventory every auth path (web, mobile, deep links); do not reinvent tokens/password storage; treat recovery like login for anti-automation; re-auth for email/2FA/password changes; MFA where possible; API keys authenticate **clients**, not end users. Detail: [3](./3_Access_Control_And_Authentication.md) + [Identity](../Identity/README.md).

### API3 — BOPLA

Vulnerable if the API **returns** sensitive properties the user should not read, or **accepts** changes to properties they should not write.

| Failure | Example |
|---------|---------|
| Over-expose | Report-user GraphQL returns `fullName` + `recentLocation` of the reported user |
| Mass assign | Host `approve_booking` body adds `total_stay_price` |
| Status flip | User sets `blocked: false` on their own moderated video |

**Break it:** cherry-pick response fields (avoid naive `to_json()` of whole models); allowlist writable properties; schema-validate responses; return the minimum business needs.

### API4 — Unrestricted resource consumption

Missing/wrong limits on timeouts, memory, FDs, processes, upload size, ops per request (GraphQL batching), records per page, or **third-party spend**.

| Scenario class | Idea |
|----------------|------|
| Paid SMS reset | Loop `forgot_password` → burn provider balance |
| GraphQL batch uploads | Rate limit per HTTP request bypassed by array of mutations |
| Huge file pull | Cache miss + no spend alert → cloud bandwidth bill spike |

**Break it:** container/serverless resource caps; max string/array/upload sizes; rate limits tuned per endpoint; throttle expensive ops (OTP, recovery); validate pagination params server-side; configure provider **spend limits** or billing alerts.

### API5 — BFLA

Ask: can a regular user hit admin endpoints? Can they flip `GET`→`DELETE`? Can group X guess group Y’s export URL? Do **not** assume admin-ness from path prefix (`/api/admins` vs `/api/users`).

**Break it:** one consistent authZ module; deny by default; explicit grants per function; admin controllers inherit admin checks; admin actions inside “regular” controllers still check role/group.

### API6 — Sensitive business flows

Not necessarily a missing `if` — excessive **legitimate** use harms the business: scalping limited stock, spam posts, booking all seats then canceling, automating referral abuse.

```text
Identify harm-if-automated flows (business)
  → Engineer friction: device signals, CAPTCHA/biometrics,
    non-human timing patterns, proxy/Tor policy where justified
  → Machine-consumed B2B APIs need stricter gates
```

### API7 — SSRF (Server-Side Request Forgery) in APIs

APIs fetch remote resources without validating the client-supplied URI → requests to unexpected destinations (even behind firewall/VPN). Basic SSRF (response returned) is easier than blind SSRF. Modern features make this common: webhooks, URL file fetch, custom SSO (single sign-on), URL previews. Cloud/K8s/Docker control planes over HTTP on well-known paths make it more dangerous.

| Scenario | Idea |
|----------|------|
| Profile `picture_url` | `localhost:8080` → timing-based internal port scan |
| Webhook “send test request” | Point URL at `169.254.169.254/.../security-credentials/...` and read credentials from the echoed response |

**Break it:** isolate fetchers so they cannot reach internal nets when the feature is “remote only”; allowlist origins/schemes/ports/media types; disable redirects; use a well-tested URL parser; sanitize input; **never** return raw upstream responses to clients. Deepen Case 1/2 mechanics in [3](./3_Access_Control_And_Authentication.md).

### API8 — Security misconfiguration

Vulnerable across the stack when: hardening missing; cloud perms wrong; unpatched systems; unnecessary features/verbs/logging; inconsistent HTTP parsing across proxies (desync); no TLS (Transport Layer Security); missing cache/security headers; bad CORS; errors with stack traces.

| Scenario | Idea |
|----------|------|
| Log template + JNDI defaults | Attacker puts `${jndi:ldap://…}` in a header that gets logged → RCE-class pull |
| DM API without `Cache-Control` | Private messages cached in browser → recovered from disk |

**Break it:** repeatable hardening; review orchestration/API/cloud configs; continuously assess config effectiveness; TLS for client↔API **and** downstream hops; allow only needed HTTP verbs; CORS + security headers for browser clients; restrict content types; uniform request parsing across the proxy chain; schema-enforce error bodies (no traces). Treat config like code ([2](./2_OWASP_Top_10_2025_In_Practice.md) A02).

### API9 — Improper inventory management

**Documentation blindspot:** unclear env (prod/stage/dev), who may reach the host, which version, missing/outdated docs, no retirement plan, stale host inventory.  
**Data-flow blindspot:** sensitive share to third parties without justification, inventory, or field-level visibility.

Classic fail: rate limits only on `api.` while `beta.api.` runs the same reset flow unprotected; or partner apps over-scoped to friends’ private data.

**Break it:** inventory hosts + integrated services + data flows; auto-generate docs in CI (OpenAPI); restrict who can read docs; protect **all** exposed versions; never give non-prod weaker security if it shares prod data; risk-analyze old versions when new ones harden security.

### API10 — Unsafe consumption of APIs

Developers trust famous third parties more than end users → weaker validation. Vulnerable if: cleartext to partners; no sanitize of pulled data; blind redirects; unbounded processing of partner payloads; no timeouts.

| Scenario class | Idea |
|----------------|------|
| Enrichment SQLi (SQL injection) | Partner stores payload; your API pulls it into SQL |
| Redirect exfil | Compromised partner returns `308` → you replay PHI to attacker |
| “Safe” name injection | Repo/name from integration concatenated into queries |

**Break it:** assess provider posture; TLS everywhere; validate/sanitize inbound partner data like user input; allowlist redirect targets; timeouts + resource caps on partner calls.

### REST / GraphQL notes

| Topic | Practice |
|-------|----------|
| GraphQL batching | Limits must apply to **operations**, not only HTTP requests |
| Introspection | Disable or restrict in production if not required |
| REST | Explicit DTOs for in/out; no entity dump |
| Webhooks | SSRF + API10 controls on callback URLs and bodies |

### Cross-walk: API risk → web Top 10 → build/test

| API | Closest Top 10:2025 | Build / verify |
|-----|---------------------|----------------|
| API1 / API5 | A01 | C1; ASVS V8/V4; WSTG (Web Security Testing Guide) 4.5 |
| API2 | A07 | C7; ASVS V6/V7/V9; WSTG 4.4–4.6 |
| API3 | A01 (+ mass assign) | C1/C3; schema allowlists |
| API4 / API6 | A10 / A06 themes | C3/C4 anti-automation; WSTG 4.10 |
| API7 | A01 (SSRF) | C10; note 3 Case 1/2 |
| API8 | A02 | C5; WSTG 4.2 |
| API9 | A03/A08 inventory flavor | SBOM-ish for APIs; retirement plan |
| API10 | A05/A08 trust boundary | Treat partner I/O as untrusted ([4](./4_Injection_And_Output_Encoding.md)) |

Program / CI ownership: literacy here → requirements in [6](./6_ASVS_Verification_Practice.md) → gates in [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

### What “done” looks like for one API

```text
Inventory (API9) complete for hosts/versions
  → Authn flows listed + rate-limited (API2)
  → Every id-taking handler has object authZ test (API1)
  → Read/write property allowlists (API3)
  → Function/admin matrix + deny-default (API5)
  → Caps + spend alerts (API4); anti-automation on harm flows (API6)
  → Fetch/webhook policy (API7); hardened config (API8)
  → Partner responses validated (API10)
```

## Apply

1. List every endpoint that takes an object id; mark BOLA check present/absent; add swap-id tests.  
2. For one resource, document allowed **read** and **write** properties (BOPLA).  
3. Map admin vs user functions; attempt verb/path guessing in an authorized lab.  
4. Cap pagination, upload size, and one paid third-party call; confirm spend alert.  
5. Name three business flows that hurt if automated; pick one anti-automation control each.  
6. Inventory API hosts/versions/envs and one sensitive third-party data flow.  
7. Trace one outbound integration: TLS, validate response, redirect policy, timeout.  
8. Cross-check ASVS V4 requirements for the same API ([6](./6_ASVS_Verification_Practice.md)).

### Self-check

- [ ] Distinguish BOLA, BFLA, and BOPLA in one sentence each  
- [ ] Explain why JWT `sub` == path userId is not a complete BOLA fix  
- [ ] Narrate GraphQL batching vs naive rate limits  
- [ ] List API4 limit types including third-party spend  
- [ ] Give one API6 example that is “authorized but harmful”  
- [ ] Define documentation vs data-flow blindspots (API9)  
- [ ] Treat partner API output as untrusted (API10)  

## Next

- [ASVS verification practice](./6_ASVS_Verification_Practice.md) · [Proactive controls and testing literacy](./7_Proactive_Controls_And_Testing_Literacy.md)

## Further reading

- [OWASP API Security Top 10:2023](https://owasp.org/API-Security/editions/2023/en/0x11-t10/)
- [API Security Top 10 — Introduction](https://owasp.org/API-Security/editions/2023/en/0x03-introduction/)
- [API1:2023 BOLA](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
- [API3:2023 BOPLA](https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/)
- [API5:2023 BFLA](https://owasp.org/API-Security/editions/2023/en/0xa5-broken-function-level-authorization/)
- [API2:2023 Broken Authentication](https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/)
- [API4:2023 Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/)
- [API6:2023 Sensitive Business Flows](https://owasp.org/API-Security/editions/2023/en/0xa6-unrestricted-access-to-sensitive-business-flows/)
- [API7:2023 SSRF](https://owasp.org/API-Security/editions/2023/en/0xa7-server-side-request-forgery/)
- [API8:2023 Security Misconfiguration](https://owasp.org/API-Security/editions/2023/en/0xa8-security-misconfiguration/)
- [API9:2023 Improper Inventory Management](https://owasp.org/API-Security/editions/2023/en/0xa9-improper-inventory-management/)
- [API10:2023 Unsafe Consumption of APIs](https://owasp.org/API-Security/editions/2023/en/0xaa-unsafe-consumption-of-apis/)
- [REST Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
- [GraphQL Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html)
- [SSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)
- [OWASP API-Security GitHub](https://github.com/OWASP/API-Security)
