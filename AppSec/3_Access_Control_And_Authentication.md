# Access control and authentication

[← AppSec](./README.md)

> **Progression:** concept → advanced → apply.

How **authorization** and **authentication** fail in web apps and APIs — and how to build them so Top 10 **A01** / **A07** (and SSRF under A01) do not become incidents. Catalog context: [2](./2_OWASP_Top_10_2025_In_Practice.md). Federation depth: [Identity](../Identity/README.md). API object-/function-/property-level patterns continue in [5](./5_API_Security_Top_10_2023.md).

Authentication answers *who is this?* Authorization answers *may they do this to that resource?* Conflating the two is how “logged in” becomes “allowed everything.”

```text
Request
  → Authenticate (identity / session / token)
  → Authorize (this subject + this action + this object + environment)
  → Act or deny (403) + log (+ alert if repeated)
```

## Concept

### Why A01 stays #1

In Top 10:2025, **broken access control** keeps #1: roughly **100%** of applications tested showed some form of it. The category maps **~40 CWEs** (including exposure of sensitive data, **CSRF**, **SSRF**, path traversal, missing/incorrect authorization). Highest occurrence count in the contributed data; second-highest related CVE pile. Treat authZ tests as mandatory, not polish.

**A07 Authentication Failures** stays #7 (~36 CWEs): stuffing, hard-coded credentials, session fixation, weak recovery, missing MFA, broken audience/scope checks — despite better frameworks.

### Least privilege and deny by default

Grant only the minimum privileges needed — **horizontally** (accountant ≠ sales) and **vertically** (manager ≠ IC). When no rule matches, **deny**. Never ship “open unless blocked.”

| Design step | Outcome |
|-------------|---------|
| Enumerate user types, resources, operations | Explicit permission matrix |
| For ABAC, include environment attributes | Time, network, device, training status |
| Justify every grant | Assume deny; prove allow |
| Tests for the matrix | CI catches missing checks |
| Periodic privilege review | Detect privilege creep after deploy |

Prefer configuring deny-by-default **explicitly** over trusting a framework’s current defaults — library defaults change.

### Validate permissions on every request

One missed check is enough. AJAX, mobile clients, and `curl` bypass the UI. Enforce authZ in **server-side** filters/middleware applied globally — not optional per-handler copy-paste.

| Do | Don’t |
|----|-------|
| Global filter / middleware (Spring Security filters, Django/Laravel middleware, .NET filters) | “We checked it on the page that links here” |
| Check the **specific** object | “User can read *some* orders ⇒ read *this* order” |
| Re-check on mutating verbs | AuthZ only on `GET` |
| Server / gateway / serverless | Client-side hide/disable as the real gate |

### Authn vs authZ (keep the boundary)

| Concern | Owns |
|---------|------|
| Passwords, MFA, sessions, token issuance | Authentication (A07) |
| Object ownership, roles/attributes, admin functions | Authorization (A01) |
| Who the IdP is, OIDC/SAML wiring | [Identity](../Identity/README.md) |

A valid session is necessary but not sufficient for privileged actions. Prefer not logging frontend users into the same IdP/AD used for internal backend/admin planes.

### Horizontal vs vertical escalation

| Class | Meaning | Typical bug |
|-------|---------|-------------|
| **Horizontal** | Same privilege tier; another user’s object | IDOR / BOLA — swap `orderId` |
| **Vertical** | Gain higher privilege (user → admin) | Force-browse admin API; tamper role claim |

Both are access-control failures. Logging without attribution makes both harder to detect after the fact.

## Advanced

### IDOR / user-controlled keys (CWE-639)

Apps expose lookup IDs (`acct_id`, PK, UUID) in query params, paths, or “hidden” fields. Attackers change the value. **Guess-resistant IDs alone are not a control** — security through obscurity fails when the ID leaks or is enumerable.

**How it happens:**

```text
GET /accountTransactions?acct_id=901   (victim’s session)
Attacker changes → acct_id=523
  → If server only loads by id, no ownership check → horizontal escalation
```

Official Top 10 scenario shape: even a *parameterized* SQL call is unsafe if the `acct` value is never checked for ownership — binding prevents injection, not IDOR.

**Break it:**

| Control | Practice |
|---------|----------|
| Prefer implicit ownership | Resolve account from the authenticated subject, not a client id |
| Indirect references | Map per-session surrogate ids → real keys (ESAPI-style) |
| Explicit ownership check | Every read/write: `resource.owner == subject` (or policy allows) |
| Function-level checks | Admin APIs require admin capability, not just “any logged-in user” |
| Never “session userId == path userId” only | Sufficient only for a tiny subset of object graphs |

BOLA (API1) is the same class when the client is an API — deepen in note 5. Comparing JWT `sub` to a path user id does **not** fix every BOLA case.

### Force browsing, verb gaps, path traversal

| Shape | Example |
|-------|---------|
| Guess admin URL | `/admin_getappInfo` without admin role |
| UI-only gate | Button hidden; `curl` still succeeds |
| Verb gap | `GET /orders/1` checked; `DELETE /orders/1` open |
| Path traversal / web-root secrets | `../`, `.git`, backups under the document root |

Treat every route and method as public until authZ proves otherwise. Disable directory listing; keep metadata and backups out of web roots.

### RBAC vs ABAC vs ReBAC (pick early)

| Model | Decision basis | Fit |
|-------|----------------|-----|
| **RBAC** | Roles assigned to users | Simple apps; watch role explosion and multi-role check chains |
| **ABAC** | Subject / object / environment attributes + policies | Fine-grained, multi-factor decisions (time, device, training) |
| **ReBAC** | Relationships (owner, friend, org member) | Social / multi-tenant “only creator can edit” |

RBAC alone struggles with horizontal object control and multi-tenant / cross-org access. ABAC/ReBAC usually scale better — but only if you design the policy model **before** coding role checks everywhere. Do not let a library’s capabilities define your requirements; requirements first, then map the tool.

### Tokens, sessions, CORS, static assets

| Topic | Practice |
|-------|----------|
| Sessions | New high-entropy id **after login**; not in URL; secure cookie; invalidate on logout / idle / absolute timeout |
| JWT | Short-lived access tokens; validate `aud` / `iss` / scopes; refresh + revoke for longer life |
| CORS | Minimize; never `*` with credentials; allowlist trusted origins |
| Static assets | Buckets/files in the **same** access policy as APIs — public only when classified public |
| Sensitive features | Re-authenticate (password / MFA / bound authenticator) before email/password/payment changes |

### Fail closed when authZ denies

Failed checks are normal. Handle them centrally and safely:

| Do | Don’t |
|----|-------|
| Consistent 403 (or 401 when unauthenticated) | Leave app in an undefined state after a failed check |
| Generic client message | Dump stack traces, SQL, or internal paths |
| Log denials with enough context for forensics | Log secrets or PII in clear |

Clock-sync and parseable log formats matter when you later join authZ denials into SIEM ([Defensive-Ops](../Defensive-Ops/README.md)).

### SSRF (under A01) — how it happens

The app fetches a URL the attacker influences. The **server** becomes a proxy into metadata services, internal HTTP APIs, or non-HTTP schemes (`file://`, `gopher://`, …) — resources never meant for the internet. First hop is often HTTP; the second request may use another protocol. XXE can also become SSRF.

```text
Attacker-controlled URL field / webhook / import / avatar fetch
  → App server requests it
    → 169.254.169.254 (cloud metadata) / internal HR API / localhost
```

**Case 1 — destinations known (allowlist viable):**

| Step | Practice |
|------|----------|
| Prefer host/IP input, not full attacker URL | App builds scheme/port/path itself |
| Validate format with battle-tested libraries | Then **strict allowlist** compare (case-sensitive) |
| Resolve DNS → check IP | Against permitted networks; **connect only to validated address** |
| Preserve Host / SNI / cert verify | Client must use validated IP without dropping TLS identity checks |
| Redirects | Disable, or re-validate every hop (scheme + DNS + IP) |
| DNS rebinding | Re-check at connect time; avoid TOCTOU second lookup |

**Case 2 — any external URL required (webhooks):** allowlists may be impossible — then block private/link-local/metadata ranges, restrict schemes to `http`/`https`, short timeouts, no open redirect chains. Deny-lists alone are bypass-prone (encoding tricks, DNS pinning, redirects).

**Cloud:** treat instance metadata as high value; prefer session-authenticated metadata APIs (e.g. IMDSv2-style) and disable legacy open metadata where you control the platform. Never allowlist metadata hosts.

### Authentication failures (A07) — how it happens

| Weakness | Attacker move |
|----------|---------------|
| No anti-automation | Credential stuffing / password spray / hybrid increments (`Winter2025`→`Winter2026`) |
| Default or weak passwords | `admin`/`Password1` |
| Known-breached passwords allowed | Reuse of leaked creds |
| Weak recovery (“secret questions”) | Account takeover without password |
| Plain / weak password storage | Offline crack after DB leak ([Cryptography](../Cryptography/README.md)) |
| Missing MFA / weak MFA fallback | Stolen password = full account |
| Session id in URL / reused after login | Fixation, leakage via Referer |
| Logout incomplete (esp. SSO without SLO) | Next user of shared browser inherits session |
| Wrong `aud` / `iss` / scope | Token accepted outside intended audience |

**Prevention spine (builder):**

| Control | Practice |
|---------|----------|
| MFA | Enforce on important systems; avoid weak fallbacks |
| No default creds | Especially admin; never expose internal service accounts on public UI |
| Password quality | Min length (NIST 800-63B: treat &lt;8 with MFA / &lt;15 without MFA as weak); max ≥64 for passphrases; **no** composition theater; block top weak + breached lists |
| Rotation | **Do not** force periodic rotation without suspected breach; reset immediately on compromise |
| Uniform errors | Same message for bad user/password — reduce enumeration (registration/recovery/API too) |
| Rate limit / delay | Failed logins; log + alert stuffing patterns; avoid turning into DoS |
| Session manager | Server-side; new id after login; secure cookie storage |
| Prefer hardened IdP | Buy/transfer risk where possible ([Identity](../Identity/README.md)) |
| Token claims | Validate audience, issuer, scopes |
| Password change | Require current password (or MFA) on an active session |
| Transport | Login + authenticated pages only over TLS |

**Scenario classes (authorized education):** stuffing without defenses → password oracle; password-only critical systems + forced complexity/rotation → reuse; public PC / incomplete SSO logout → next person inherits access.

### CSRF (still access control)

Browser sends cookies to your origin while the user visits attacker content. State-changing requests need synchronizer tokens / SameSite discipline / origin checks — not “secret URL” alone. SameSite is helpful but not a complete CSRF strategy by itself; pair with anti-CSRF tokens for sensitive mutations. Re-auth for high-risk actions further reduces CSRF/session-theft impact.

```text
Victim authenticated to bank.example
  → Visits attacker page
    → Browser auto-sends bank cookies on forged POST
      → Server MUST require CSRF token / SameSite+origin proof / re-auth
```

## Apply

1. Draw a permission matrix (role/attribute × resource × verb) for one product area.  
2. Find every API that takes an object id; mark ownership check present/absent.  
3. Add CI tests: swap id (IDOR), force-browse admin, `DELETE` without role, deny-by-default path.  
4. Trace one URL-fetch feature; document Case 1 allowlist or Case 2 block policy + redirect handling.  
5. Confirm MFA on admin and high-value user flows; check password min length + breached-password check.  
6. Verify logout invalidates server session (and SSO SLO if applicable); confirm new session id after login.  
7. Log authZ denials; alert on repetition; ensure failure paths return safe errors.  
8. Inventory static buckets/CDN paths against the same classification policy as APIs.  
9. Schedule Identity note for OIDC/SAML wiring if you federate.

### Self-check

- [ ] Separate authn from authZ in one sentence each  
- [ ] Narrate IDOR and UI-only authZ chains  
- [ ] State deny-by-default and every-request checks  
- [ ] Compare RBAC vs ABAC/ReBAC for object-level control  
- [ ] Explain SSRF Case 1 (allowlist + DNS/IP revalidation) vs Case 2  
- [ ] List A07 stuffing/MFA/session/NIST password controls  
- [ ] Know why CSRF is an access-control problem  
- [ ] Explain why parameterized SQL still fails without ownership checks  

## Next

- [Injection and output encoding](./4_Injection_And_Output_Encoding.md) · [API Security Top 10:2023](./5_API_Security_Top_10_2023.md)

## Further reading

- [A01:2025 Broken Access Control](https://owasp.org/Top10/2025/A01_2025-Broken_Access_Control/)
- [A07:2025 Authentication Failures](https://owasp.org/Top10/2025/A07_2025-Authentication_Failures/)
- [Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
- [Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [SSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)
- [Cross-Site Request Forgery Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
- [NIST SP 800-63B — Digital Identity (memorized secrets)](https://pages.nist.gov/800-63-3/sp800-63b.html)
- [NIST SP 800-162 — ABAC](https://csrc.nist.gov/pubs/sp/800/162/final)
- [Identity](../Identity/README.md)
