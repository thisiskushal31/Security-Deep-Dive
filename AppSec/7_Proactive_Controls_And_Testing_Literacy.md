# Proactive controls and testing literacy

[← AppSec (application security)](./README.md)

> **Progression:** concept → advanced → apply.

How to **build** the right controls early (**OWASP Top 10 Proactive Controls 2024**) and **verify** running apps with literate use of the **Web Security Testing Guide (WSTG v4.2)** — without turning either into an exploit cookbook. Program spine: [1](./1_Secure_SDLC_And_AppSec_Program.md). Risks: [2](./2_OWASP_Top_10_2025_In_Practice.md). Requirements yardstick: [6](./6_ASVS_Verification_Practice.md). Deep PTES (Penetration Testing Execution Standard) / lab exploitation: [Offensive](../Offensive/README.md). CI gate wiring: [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

Top 10 says what goes wrong. Proactive controls say what to implement first. ASVS says what “verified” means. WSTG says how to test the running system. Use all four; do not collapse them into one list.

```text
Design / code (C1–C10)
  → Requirements proof (ASVS v5.0.0-…)
  → Risk awareness (Top 10 / API Top 10)
  → Test literacy (WSTG scenarios → tickets / CI / Offensive handoff)
```

## Concept

### Proactive vs reactive

| Mode | Feel | Cost |
|------|------|------|
| **Reactive** | Vuln in prod → scramble patch → re-release | High; reputational + emergency engineering |
| **Proactive** | Positive patterns at inception (authZ, validation, logging, secure defaults) | Lower long-run; still not a bug-free guarantee |

Proactive Controls 2024 are ordered techniques every architect/developer should know. They reduce chance of common issues; they do **not** promise zero defects — the alternative to shipping code is not shipping.

Each control page uses the same shape: Description → Threat(s) → Implementation → Vulnerabilities Prevented → References → Tools.

### C1–C10 (2024) — builder map

| # | Control | Builder habit | Deepen |
|---|---------|---------------|--------|
| **C1** | Implement Access Control | Design authZ up front; PEP on every request; deny by default; least privilege / JIT-JEA; prefer permission checks over hard-coded role soup | [3](./3_Access_Control_And_Authentication.md), [5](./5_API_Security_Top_10_2023.md) |
| **C2** | Use Cryptography to Protect Data | Managed crypto; TLS (Transport Layer Security); no homemade ciphers | [Cryptography](../Cryptography/README.md) |
| **C3** | Validate all Input & Handle Exceptions | Syntactic then semantic validation; allowlist > denylist; server-side always; parametrize / encode; fail closed | [4](./4_Injection_And_Output_Encoding.md), A10 in [2](./2_OWASP_Top_10_2025_In_Practice.md) |
| **C4** | Address Security from the Start | KISS; secure defaults; minimize attack surface; defense-in-depth; threat model; secure architecture patterns | [1](./1_Secure_SDLC_And_AppSec_Program.md), [Threat-Modeling](../Threat-Modeling/README.md) |
| **C5** | Secure By Default Configurations | Locked-down baselines; explicit opt-in to risk | A02 in [2](./2_OWASP_Top_10_2025_In_Practice.md), API8 in [5](./5_API_Security_Top_10_2023.md) |
| **C6** | Keep your Components Secure | Inventory, patch, pin, SBOM (Software Bill of Materials); do not fight the framework | A03 in [2](./2_OWASP_Top_10_2025_In_Practice.md), [Cloud-Security](../Cloud-Security/README.md) |
| **C7** | Secure Digital Identities | MFA (multi-factor authentication), sessions, federation done right | [3](./3_Access_Control_And_Authentication.md), [Identity](../Identity/README.md) |
| **C8** | Leverage Browser Security Features | Cookie flags, CSP, security headers — defense in depth, not sole XSS (Cross-Site Scripting) fix | [4](./4_Injection_And_Output_Encoding.md) |
| **C9** | Implement Security Logging and Monitoring | Security events + **alerts** + response; no secrets in logs | A09 in [2](./2_OWASP_Top_10_2025_In_Practice.md) |
| **C10** | Stop Server Side Request Forgery | Allowlist / isolate fetchers; secure XML parsers | [3](./3_Access_Control_And_Authentication.md), API7 in [5](./5_API_Security_Top_10_2023.md) |

**2018 → 2024:** the list was reorganized (e.g. 2018 “Define Security Requirements” / “Encode and Escape” / “Handle Exceptions” folded into broader 2024 themes). Prefer **2024** names in new standards; map old training decks deliberately.

### WSTG literacy (what it is / is not)

| Is | Is not |
|----|--------|
| Consensus testing **techniques** for web apps/services | Exhaustive CVE (Common Vulnerabilities and Exposures) checklist |
| Role guide for developers, QA, security, PMs | Substitute for ASVS requirements |
| Place to start; tailor and re-prioritize | Blind “complete every test” mandate |
| Complements SAST (Static Application Security Testing)/DAST (Dynamic Application Security Testing)/SCA (Software Composition Analysis) | Proof you are secure if scanners are green |

Stable citation release today: **v4.2** ([wstg.owasp.org](https://wstg.owasp.org/)). “Latest” tracks work toward 5.0 — cite **versioned** links for contracts and tickets.

**ID forms:**

| Form | Example |
|------|---------|
| Category + number | `WSTG-INFO-02` |
| Versioned | `WSTG-v42-INFO-02` |
| URL | `https://wstg.owasp.org/v4.2/4-Web_Application_Security_Testing/01-Information_Gathering/02-Fingerprint_Web_Server/` |

Prefer versioned URLs — `stable` / `latest` move under you.

### Testing chapters (v4.2 map)

| § | Theme | Use when probing… |
|---|--------|-------------------|
| **4.1** | Information Gathering | Fingerprint, entry points, map |
| **4.2** | Configuration & Deployment | Hardening, default creds, cloud buckets |
| **4.3** | Identity Management | Registration, account provisioning |
| **4.4** | Authentication | Login, MFA, recovery ([3](./3_Access_Control_And_Authentication.md)) |
| **4.5** | Authorization | IDOR (Insecure Direct Object Reference), vertical/horizontal, BFLA (Broken Function Level Authorization) |
| **4.6** | Session Management | Fixation, timeout, cookies |
| **4.7** | Input Validation | Injection / XSS families ([4](./4_Injection_And_Output_Encoding.md)) |
| **4.8** | Error Handling | Info leaks, fail-open |
| **4.9** | Weak Cryptography | TLS, storage ([Cryptography](../Cryptography/README.md)) |
| **4.10** | Business Logic | Abuse cases, API6-class flows |
| **4.11** | Client-side | DOM XSS, browser storage, CORS |

API-heavy products still use these chapters plus API Top 10 scenarios in [5](./5_API_Security_Top_10_2023.md). Full adversarial methodology lives in [Offensive](../Offensive/README.md) — this note stops at **literacy and handoff**.

## Advanced

### C1 — access control (implementation spine)

From the official control: design thoroughly up front (RBAC (Role-Based Access Control) vs ABAC (Attribute-Based Access Control)); force every request through a Policy Enforcement Point; **one** consolidated check library; deny by default (including exceptions during authZ); least privilege / JIT-JEA; avoid fragile `hasRole("ADMIN")` forests — prefer permission/attribute checks that survive multi-tenancy.

Align **all** paths (UI, API, jobs, admin tools) to the same checks — weakest path wins for the attacker.

### C3 — validation & exceptions (implementation spine)

| Rule | Practice |
|------|----------|
| Order | Syntactic validity → semantic validity → use |
| Allowlist | Prefer known-good; denylist only as detection aid |
| Where | Server-side for security; client-side for UX |
| Regex | Useful; watch ReDoS; sometimes plain code is clearer |
| Mass assignment | DTOs or allowlisted bind fields ([5](./5_API_Security_Top_10_2023.md) BOPLA (Broken Object Property Level Authorization)) |
| Limits | Valid email can still be SQLi (SQL injection) — validation ≠ primary injection defense |
| Separate data/commands | Prepared statements, safe APIs, no SSTI of user input |
| HTML | Sanitize with a real library when markup is required |
| Deserialize | Prefer JSON; never trust hostile serialization blindly |
| Exceptions | Fail closed; safe errors (A10) |

### C4 / C5 / C6 — architecture, defaults, components

| Control | Concrete |
|---------|----------|
| **C4** | Minimize attack surface; no security-by-obscurity; trust boundaries enforced; reuse reviewed secure architecture patterns |
| **C5** | Path of least resistance = secure; risky options require explicit enable |
| **C6** | Gain from maintained frameworks; accept the patch/SBOM burden; do not fight framework security features |

### C9 — logging that can stop an attack

Log malicious-looking activity (out-of-range data, tampered limited fields, authZ violations). At minimum mark high severity; ideally respond (invalidate session / lock). Secure the logger: encode to prevent log injection; never log passwords/session IDs/PAN/SSN; protect integrity; centralize. Time-sync nodes. Pair with **alerting** owners — logs alone ≈ A09 failure.

### C5–C8 — defaults, deps, identity, browser

| Control | Concrete builder moves |
|---------|------------------------|
| **C5** | Secure baselines for frameworks, containers, cloud; disable unused verbs/features; no default admin passwords; config review in CI |
| **C6** | Pin versions; SCA in PR; remove unused deps; prefer maintained frameworks with secure defaults; subscribe to advisories |
| **C7** | MFA on sensitive apps; secure session lifecycle; prefer hardened IdP (identity provider); re-auth for account changes ([3](./3_Access_Control_And_Authentication.md), [Identity](../Identity/README.md)) |
| **C8** | Cookie flags + response headers below — still encode for XSS |

### C8 — header / cookie checklist (builder)

| Control | Practice |
|---------|----------|
| Cookies | `Secure`, `HttpOnly`, appropriate `SameSite`; no session id in URL |
| CSP | Real policy for HTML apps; not a REST JSON substitute for encoding |
| HSTS | Long `max-age`; understand lock-out risk before preload |
| `nosniff` | Always on responses that browsers might sniff |
| Frames | CSP `frame-ancestors` (prefer) or `X-Frame-Options: DENY` |
| Permissions-Policy | Disable unused powerful features |
| CORS | Explicit origins; disable if no browser cross-origin need |
| Avoid | `X-XSS-Protection` on; HPKP; Expect-CT |

Full table and upload-serving headers: [4](./4_Injection_And_Output_Encoding.md).

### C8 / C10 — browser features & SSRF (Server-Side Request Forgery)

CSP, `HttpOnly`, security headers shrink blast radius — never the only XSS control. SSRF: allowlist targets, isolate fetchers, harden XML against XXE-as-SSRF ([3](./3_Access_Control_And_Authentication.md)).

### Paved roads (program → code)

Official modern AppSec program guidance: give developers SAST + SCA + **secret** + **IaC (Infrastructure as Code)** scanners **and** teach them; ship **guardrails** and **paved roads** (secure authZ, CSRF (Cross-Site Request Forgery), validation libraries as the default path); secret manager + docs; security IDE plugins. Champions programs and abuse-case tests belong beside WSTG literacy ([1](./1_Secure_SDLC_And_AppSec_Program.md)).

### WSTG authorization literacy (example shape)

Authorized testing for access control (WSTG §4.5 themes) asks questions — it does not ship payload cookbooks:

| Question | Pass signal |
|----------|-------------|
| Can user A read/modify user B’s object by changing an id? | Consistent deny + log |
| Can a non-admin hit admin functions by URL/verb change? | 401/403; no data leak in body |
| Is authZ enforced server-side for every method? | `PUT`/`DELETE` same as `GET` |
| Does force-browsing authenticated-only paths fail when logged out? | Redirect/401, not 200 with data |

Map failures to C1 + `v5.0.0` V8 requirements + Top 10 A01 / API1/API5. Automate the stable cases; explore novel business-logic paths manually or via [Offensive](../Offensive/README.md) under authorization.

### Map: Control ↔ Top 10 ↔ ASVS ↔ WSTG (starter)

| Build (C) | Risk reminder | Verify (ASVS ch.) | Test literacy (WSTG §) |
|-----------|---------------|-------------------|-------------------------|
| C1 | A01, API1/5 | V8 (+ V4) | 4.5 Authorization |
| C2 | A04 | V11, V12 | 4.9 Cryptography |
| C3 | A05, A10 | V1, V2 | 4.7 Input, 4.8 Errors |
| C4 | A06 | V15 | Design / threat model first |
| C5 | A02, API8 | V13 | 4.2 Configuration |
| C6 | A03 | V13 / supply chain | SCA + config |
| C7 | A07, API2 | V6, V7, V9, V10 | 4.3–4.6 |
| C8 | XSS impact | V3 | 4.11 Client-side |
| C9 | A09 | V16 | Monitoring / detection tests |
| C10 | A01 SSRF, API7 | V1/V2/V4 | Input + config + API scenarios |

### WSTG in the program (tailor, do not checklist-worship)

Official guidance: infinite failure modes, finite time — **continuously re-prioritize** by business risk. Roles:

| Role | Use of WSTG |
|------|-------------|
| Developers | Unit/integration cases inspired by scenarios |
| QA | Expand functional tests into security cases |
| Security | Depth + judgment; tools for breadth |
| PM | Security bugs are bugs — schedule them |

**Tools:** SAST/DAST/SCA/ZAP scale baseline policy. They are generic — they miss much custom business logic. Investigate findings; balance tool time vs manual technique time. “We ran the scanner” ≠ “we tested authorization.”

```text
Pick Top 10 / API risks in scope
  → Pick ASVS requirements (v5.0.0-…)
  → Pick WSTG scenarios that evidence those requirements
  → Automate what is stable; manual what is business-logic
  → Ticket with WSTG-v42-… + ASVS id
  → CI owns gate ([DevOps-Handbook](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security))
  → Deep exploit / PTES → [Offensive](../Offensive/README.md) (authorized labs only)
```

### Authorized testing only

Reconnaissance and validation techniques in WSTG assume **permission**. Production poking without authorization is out of scope. Lab rules: [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

## Apply

1. For one product area, mark C1–C10 as Met / Partial / Missing; open three tickets.  
2. Replace one hard-coded role check with a permission/attribute check (C1).  
3. Add server-side allowlist validation + parametrized query on one risky input (C3).  
4. Confirm security logging for authZ denials **and** an alert with an owner (C9).  
5. Pick five WSTG-v4.2 scenarios that map to your hottest Top 10 risks; cite `WSTG-v42-…`.  
6. Attach one ASVS `v5.0.0-…` id to each of those five tests.  
7. Put two of the five into CI as automated regressions; leave business-logic ones manual.  
8. Document Offensive/Handbook handoff: what stays literacy here vs deep test vs pipeline gate.

### Self-check

- [ ] Explain proactive vs reactive with one release story  
- [ ] Recite C1–C10 headlines and where each deepens in this folder  
- [ ] State allowlist-before-denylist and server-side validation (C3)  
- [ ] Cite a WSTG scenario in `WSTG-v42-…` form  
- [ ] List v4.2 testing chapter themes 4.1–4.11  
- [ ] Explain why scanners + WSTG both belong in a balanced program  
- [ ] Map one control → Top 10 → ASVS → WSTG row  
- [ ] Know when to hand off to Offensive vs DevOps-Handbook  

## Next

- [AppSec README](./README.md) · [Secure SDLC program](./1_Secure_SDLC_And_AppSec_Program.md) · [ASVS](./6_ASVS_Verification_Practice.md)

## Further reading

- [OWASP Top 10 Proactive Controls 2024](https://top10proactive.owasp.org/the-top-10/)
- [C1: Implement Access Control](https://top10proactive.owasp.org/archive/2024/the-top-10/c1-accesscontrol/)
- [C3: Validate all Input & Handle Exceptions](https://top10proactive.owasp.org/archive/2024/the-top-10/c3-validate-input-and-handle-exceptions/)
- [C4: Address Security from the Start](https://top10proactive.owasp.org/archive/2024/the-top-10/c4-secure-architecture/)
- [C9: Security Logging and Monitoring](https://top10proactive.owasp.org/archive/2024/the-top-10/c9-security-logging-and-monitoring/)
- [C10: Stop SSRF](https://top10proactive.owasp.org/archive/2024/the-top-10/c10-stop-server-side-request-forgery/)
- [HTTP Headers Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
- [OWASP Top 10:2025 — Modern Application Security Program](https://owasp.org/Top10/2025/0x03_2025-Establishing_a_Modern_Application_Security_Program/)
- [WSTG home](https://wstg.owasp.org/)
- [WSTG v4.2 — Web Application Security Testing](https://wstg.owasp.org/v4.2/4-Web_Application_Security_Testing/)
- [WSTG project page](https://owasp.org/www-project-web-security-testing-guide/)
- [ASVS 5.0](https://asvs.owasp.org/)
- [DevOps-Handbook — Security gate chain](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/4_Security_Gate_Chain.md)
- [Foundations — Ethics / authorization / lab rules](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md)
