# ASVS verification practice

[← AppSec](./README.md)

> **Progression:** concept → advanced → apply.

How to use **OWASP ASVS 5.0.0** (published 30 May 2025) as a requirements and verification yardstick — not as a random checklist dump. Program frame: [1](./1_Secure_SDLC_And_AppSec_Program.md). Risks: [2](./2_OWASP_Top_10_2025_In_Practice.md). API slice: [5](./5_API_Security_Top_10_2023.md).

ASVS normalizes what “verified” means for web apps and web services: a commercially workable open standard for testing technical controls and for telling developers what to build. Use it as a **metric**, as **guidance** for control design, and as **procurement** language in contracts.

```text
Pick target level (risk-based)
  → Filter chapters/sections that apply
  → Cite requirements as v5.0.0-<chapter>.<section>.<req>
  → Verify (design → implement → test) → track gaps
```

## Concept

### What 5.0.0 is

| Fact | Detail |
|------|--------|
| Stable release | **5.0.0** — May 2025 (PDF / DOCX / CSV) |
| Scale | **~345** requirements in **17** chapters (CSV: 345 rows) |
| Model | Three levels retained; level **definitions evolved** for adoption |
| L1 share | **~70** L1 (~**20%**) — down from ~46% L1 in 4.0.3, on purpose |
| L2 share | ~**50%** of requirements tagged L2 → complying with L2 means ~**70%** of the standard (all L1+L2) |
| L3 share | Remaining ~**30%** — high assurance / hard controls |

Major version bumps (4.x → 5.0.0) can renumber everything — **always include the version** in citations.

### Levels (5.0 meaning)

| Level | Intent | When |
|-------|--------|------|
| **L1** | Minimum first-layer defenses; low barrier to entry; ~20% of reqs | Starting point; e.g. early product with limited sensitive data |
| **L2** | Where **most applications should aim**; less common attacks or deeper protections | Default target for serious internet apps |
| **L3** | Highest assurance; defense-in-depth / hard-to-implement controls | High sensitivity (e.g. banking) when users expect it |

Levels are **priority-based** (risk reduction vs effort), not a claim that every org must hit L3. An org analyzes risk and chooses; ASVS does not prescribe one level for all apps. Requirements are cumulative in practice: claiming L2 means L1+L2.

L1 is not always fully black-box testable without docs/code — fewer reqs still make verification easier than 4.x’s large L1 set.

### How to cite a requirement

Format: **`v5.0.0-<chapter>.<section>.<requirement>`** (lowercase `v`).

Example: **`v5.0.0-1.2.5`** = 5th requirement in **Injection Prevention** (section 1.2) of **Encoding and Sanitization** (chapter 1): protect against OS command injection with parameterized OS queries or contextual encoding.

| Piece | Meaning |
|-------|---------|
| `1` | Chapter V1 Encoding and Sanitization |
| `2` | Section Injection Prevention |
| `5` | Specific requirement |

Bare `1.2.5` without a version should be read as “latest” — that becomes wrong across major releases. Prefer the full `v5.0.0-…` form in tickets, contracts, and scanner mappings.

### Chapter map (filter what applies)

| Ch | Name | ~Reqs | Use when |
|----|------|------:|----------|
| **V1** | Encoding and Sanitization | 30 | Any app with interpreters / XSS/SQLi surfaces ([4](./4_Injection_And_Output_Encoding.md)) |
| **V2** | Validation and Business Logic | 13 | Input rules, transactions, anti-automation |
| **V3** | Web Frontend Security | 31 | Browser apps — skip for pure M2M APIs |
| **V4** | API and Web Service | 16 | REST/GraphQL/SOAP/WebSocket APIs ([5](./5_API_Security_Top_10_2023.md)) |
| **V5** | File Handling | 13 | Uploads, downloads, storage |
| **V6** | Authentication | 47 | Login, passwords, MFA ([3](./3_Access_Control_And_Authentication.md)) |
| **V7** | Session Management | 19 | Cookies, timeouts, fixation |
| **V8** | Authorization | 13 | Object/function authZ |
| **V9** | Self-contained Tokens | 7 | JWT-style tokens |
| **V10** | OAuth and OIDC | 36 | Federation — else skip; deepen in [Identity](../Identity/README.md) |
| **V11** | Cryptography | 24 | Algorithms, keys ([Cryptography](../Cryptography/README.md)) |
| **V12** | Secure Communication | 12 | TLS |
| **V13** | Configuration | 21 | Hardening, headers, secrets |
| **V14** | Data Protection | 13 | Privacy/sensitivity handling |
| **V15** | Secure Coding and Architecture | 21 | Design/architecture controls |
| **V16** | Security Logging and Error Handling | 17 | Logs, alerts, safe errors |
| **V17** | WebRTC | 12 | Only if you use WebRTC |

Fork or tailor from **L1** as baseline; drop irrelevant chapters (no OAuth → ignore V10; no browser → thin V3).

## Advanced

### Using ASVS in the SDLC

| Phase | Practice |
|-------|----------|
| Requirements | Paste `v5.0.0-…` IDs into user stories / non-functionals |
| Design | Threat model maps to chapters (authZ → V8; injection → V1) |
| Build | Secure coding standards cite ASVS IDs |
| Verify | Manual + automated tests tagged with requirement IDs |
| Procurement | “Must meet ASVS 5.0.0 Level 2 for in-scope chapters” |
| Audit | Gap list: requirement → evidence → owner → due date |

Pair with SSDF/SAMM program practices in [1](./1_Secure_SDLC_And_AppSec_Program.md). Do not treat a green SCA scan as “ASVS complete.”

### Worked citations (concrete, not folklore)

| ID | Level | Says (paraphrase) | Ties to |
|----|------:|-------------------|---------|
| `v5.0.0-1.2.1` | L1 | Context-correct output encoding for HTML/XML/HTTP | [4](./4_Injection_And_Output_Encoding.md) XSS |
| `v5.0.0-1.2.4` | L1 | Parameterized DB/ORM/NoSQL/Cypher queries | [4](./4_Injection_And_Output_Encoding.md) SQLi |
| `v5.0.0-1.2.5` | L1 | OS command injection protections | [4](./4_Injection_And_Output_Encoding.md) |
| `v5.0.0-1.3.1` | L1 | Sanitize untrusted HTML via known library | Rich text |
| `v5.0.0-1.5.1` | L1 | Restrictive XML parsers; no external entities | XXE → SSRF class |
| `v5.0.0-2.4.1` | L2 | Anti-automation on functions that enable abuse | API4/API6 |
| `v5.0.0-8.*` | mix | Authorization chapter | [3](./3_Access_Control_And_Authentication.md), API1/5 |
| `v5.0.0-4.*` | mix | API and Web Service chapter | [5](./5_API_Security_Top_10_2023.md) |
| `v5.0.0-10.2.1` | L2 | OAuth code flow CSRF protections (PKCE / `state`) | [Identity](../Identity/README.md) |

Always verify wording against the official PDF/CSV for the version you claim — paraphrases here are for navigation.

### Mapping Top 10 / API Top 10 → chapters (starter)

| Risk home | Start in ASVS |
|-----------|---------------|
| A01 / API1 / API5 | V8 Authorization (+ V4 for APIs) |
| A05 / injection / XSS | V1 Encoding and Sanitization |
| A07 / API2 | V6 Authentication, V7 Sessions, V9 Tokens |
| A02 / API8 | V13 Configuration, V12 Communication |
| API3 BOPLA | V4 + V8 + output minimization in V1/V14 |
| API4 / API6 | V2 (anti-automation, business logic) |
| API7 SSRF | V1/V2/V4 + network controls |
| A09 logging | V16 |
| OAuth/OIDC | V10 |

Cheat Sheet Series [IndexASVS](https://cheatsheetseries.owasp.org/IndexASVS.html) helps jump from requirement themes to how-to sheets.

### Verification practice (how to “pass” a requirement)

| Evidence type | Good | Weak |
|---------------|------|------|
| Design | ADR or threat-model note citing the ID | “We use Spring Security” with no mapping |
| Implementation | Code path + config snippet | Untested middleware “should” cover it |
| Test | Automated test named with `v5.0.0-…` | One manual poke last year |
| Negative test | Unauthorized id / verb / property rejected | Only happy-path QA |

```text
Requirement v5.0.0-x.y.z
  → Applicable? (chapter filter)
  → Implemented where?
  → Test that fails if removed?
  → Residual risk accepted? (signed)
```

### Common failure modes adopting ASVS

| Mistake | Fix |
|---------|-----|
| Claim “ASVS compliant” with no version/level/scope | State `5.0.0`, level, and in-scope chapters |
| Apply all 17 chapters to an M2M API | Filter V3/V17/etc. |
| Only L1 forever for a high-risk app | Re-assess toward L2/L3 |
| Renumbering blindness after upgrade | Re-map tools to `v5.0.0-…` |
| Scanner ≠ ASVS | Map findings to requirements; close gaps with engineering |

### Starter verification pack (L1, web + API)

Use this as a first sprint — then expand with CSV filters:

| Theme | Example IDs to open first |
|-------|---------------------------|
| Encoding / injection | `v5.0.0-1.2.1`, `1.2.4`, `1.2.5`, `1.3.1`, `1.5.1` |
| Authn / session | Open V6 + V7 L1 rows in the CSV for your stack |
| Authorization | Open V8 L1 rows; pair with BOLA/BFLA tests from [5](./5_API_Security_Top_10_2023.md) |
| API surface | Open V4 L1/L2 rows for REST/GraphQL |
| Config / TLS | V12 + V13 L1 rows |

```text
Week 1: scope + L1 applicable list
Week 2–3: implement/fix gaps for V1, V6, V8 (and V4 if API)
Week 4: evidence sheet (ID → test → owner)
Then: expand toward L2 for in-scope chapters
```

### Machine-readable workflow

1. Download official **CSV** for 5.0.0.  
2. Filter `chapter_id` ∈ scope; drop N/A with written rationale.  
3. Sort by `L` ascending — close L1 before celebrating L2.  
4. Store status beside `req_id` (Open / Partial / Met / N/A).  
5. In CI or tickets, search for the full `v5.0.0-…` string so upgrades are greppable.

### Release numbering (do not get surprised)

| Change | What happens to IDs |
|--------|---------------------|
| **Major** (4.0.3 → 5.0.0) | Full reorg possible; re-evaluate compliance |
| **Minor** (5.0.0 → 5.1.0) | Add/remove reqs; numbers mostly stable; still re-check |
| **Patch** | Clarifications; smaller delta |

Always pin the version in contracts: “ASVS **5.0.0** Level 2 for chapters V1, V4, V6–V8, V12–V13, V16.”

### ASVS ↔ Proactive Controls ↔ WSTG

| Use | Artifact |
|-----|----------|
| What to build | [Proactive Controls](./7_Proactive_Controls_And_Testing_Literacy.md) C1–C10 |
| What “met” means | This note — `v5.0.0-…` |
| How to test the running app | WSTG-v4.2 scenarios (same note 7) |
| What usually breaks | [2](./2_OWASP_Top_10_2025_In_Practice.md) / [5](./5_API_Security_Top_10_2023.md) |

Example ticket line: `Meet v5.0.0-1.2.4; evidence WSTG-v42 input-validation SQLi case; implements C3.`

## Apply

1. Choose a target: **ASVS 5.0.0 Level __** for app X; write one sentence justifying it.  
2. Cross out irrelevant chapters; keep a one-page scope list.  
3. Export/open the official CSV; filter your chapters; tag each row Applicable / N/A.  
4. Pick ten L1 requirements in V1+V6+V8; link each to code or a gap ticket using full `v5.0.0-…` IDs.  
5. Add one automated test whose name or annotation includes a requirement ID.  
6. Map your Top 10 / API Top 10 hot spots to chapters (table above).  
7. If you use OAuth, schedule V10 review with [Identity](../Identity/README.md); else mark V10 N/A with rationale.  
8. Put versioned ASVS language into the next vendor security questionnaire or contract draft.

### Self-check

- [ ] State L1 vs L2 vs L3 intent in 5.0 terms  
- [ ] Cite one requirement in `v5.0.0-x.y.z` form and name its chapter  
- [ ] Explain why 5.0 shrank L1 vs 4.x  
- [ ] Filter chapters for a pure API vs a browser app  
- [ ] Distinguish ASVS (requirements) from Top 10 (awareness)  
- [ ] List evidence types that make a requirement “verified”  

## Next

- [Proactive controls and testing literacy](./7_Proactive_Controls_And_Testing_Literacy.md) · [API Security Top 10:2023](./5_API_Security_Top_10_2023.md)

## Further reading

- [OWASP ASVS project](https://owasp.org/www-project-application-security-verification-standard/)
- [ASVS 5.0.0 site / downloads](https://asvs.owasp.org/)
- [ASVS GitHub — v5.0.0](https://github.com/OWASP/ASVS/tree/v5.0.0)
- [How to reference ASVS requirements](https://owasp.org/www-project-application-security-verification-standard/) (see “How To Reference ASVS Requirements”)
- [Cheat Sheet Series — IndexASVS](https://cheatsheetseries.owasp.org/IndexASVS.html)
- [OWASP Top 10:2025](https://owasp.org/Top10/2025/)
- [API Security Top 10:2023](https://owasp.org/API-Security/editions/2023/en/0x11-t10/)
