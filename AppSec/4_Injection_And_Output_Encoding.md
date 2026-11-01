# Injection and output encoding

[← AppSec](./README.md)

> **Progression:** concept → advanced → apply.

How **injection** works across interpreters (SQL, OS, LDAP, templates, XSS) and how to keep **data from becoming code**. Catalog: Top 10 **A05**. Access control: [3](./3_Access_Control_And_Authentication.md). LLM prompt injection is a related class in [AI-Security](../AI-Security/README.md) — classic sinks here still apply when models emit HTML/SQL/shell.

An injection flaw exists when untrusted input reaches an interpreter and parts of that input execute as commands or markup. Perfect resistance means every variable is validated and then escaped/sanitized for its **context** — or never reaches an interpreter at all.

```text
Untrusted input
  → Prefer: safe API / bound parameters (data ≠ structure)
  → Else: positive validation + context-correct encoding
  → Never: string-build queries/commands/HTML from raw input
```

## Concept

### Why A05 still matters at #5

Injection dropped two spots (2021 #3 → 2025 #5) but remains **100% tested** in contrib data, with the **largest CVE pile** of any category (~37 CWEs; tens of thousands of CVEs). XSS (CWE-79) is high frequency / lower average impact; SQL injection is lower frequency / high impact. Rank drop ≠ underfund defenses.

A related class in LLMs is **prompt injection** ([OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/) / [AI-Security](../AI-Security/README.md)) — treat model output as untrusted for these same sinks.

### When an app is injectable

| Condition | Why it hurts |
|-----------|--------------|
| No validation / sanitization of user-supplied data | Hostile bytes reach interpreters |
| Dynamic queries without parameters | Input changes SQL/command **structure** |
| ORM/HQL built by string concat | “Framework” still injectable |
| Hostile data concatenated into commands / stored procedures | Same class inside the DB engine |

Common families: SQL / NoSQL, OS command, ORM, LDAP, Expression Language / OGNL, template injection (SSTI), and **XSS** (browser as interpreter). Detection needs source review **plus** testing of parameters, headers, cookies, JSON, XML — SAST/DAST/IAST in CI help but do not replace design.

### Preference order (prevention)

1. **Safe API** — avoid the interpreter, or use a parameterized interface / ORM **without** string-built predicates.  
2. **Positive server-side validation** — allow known-good; incomplete when special characters are required.  
3. **Context-specific escaping** — last resort for residual dynamic queries; fragile under interpreter changes.

**Warnings:**

| Trap | Detail |
|------|--------|
| “Parameterized” stored procs | Still inject if they concatenate inside PL/SQL / `EXECUTE IMMEDIATE` |
| Structure names from users | Table/column names **cannot** be escaped — never take them from clients |
| Manual escape | Error-prone; prefer libraries and frameworks’ auto-escape |
| Wrong encoder for context | HTML-encode inside a JS string (or vice versa) ≈ unprotected |

## Advanced

### SQL / ORM injection — how it happens

**Vulnerable shape (education — do not ship):**

```java
String query = "SELECT * FROM accounts WHERE custID='"
    + request.getParameter("id") + "'";
// Attacker: id = ' OR '1'='1  → returns all rows
```

**Same bug inside HQL/ORM:**

```java
session.createQuery(
  "FROM accounts WHERE custID='" + request.getParameter("id") + "'");
```

**Defender shape:**

```java
PreparedStatement ps = conn.prepareStatement(
    "SELECT * FROM accounts WHERE custID = ?");
ps.setString(1, request.getParameter("id"));
// Bound value cannot change SQL structure
```

```text
Request id
  → MUST bind as parameter
  → Query plan/structure fixed by developer string only
```

**Hardening beyond bind:**

| Control | Practice |
|---------|----------|
| DB least privilege | App role: only needed tables/verbs; no DBA for runtime |
| Views | Narrow what the app account can even see |
| Allow-list for rare dynamic identifiers | Only if product forces dynamic ORDER BY / table — never free-form |
| Escaping all inputs | Strongly discouraged as primary defense |

Parameterized queries stop **injection**; they do **not** stop **IDOR** — still authorize the object ([3](./3_Access_Control_And_Authentication.md)).

### OS command, LDAP, NoSQL, SSTI

| Family | Failure shape | Break it |
|--------|---------------|----------|
| **OS command** | `Runtime.exec("nslookup " + domain)` → `; cat /etc/passwd` | Avoid shell; argv arrays / fixed binary; allowlist args |
| **LDAP** | Filter string concat | Parameterized LDAP APIs / escape per LDAP rules |
| **NoSQL** | Operator injection via JSON (`$gt`, `$where`) | Typed drivers; never merge raw user objects into queries |
| **SSTI / EL / OGNL** | User string into template engine | Never evaluate user input as template; sandbox or disallow |

### XSS — the browser as interpreter

XSS injects content the browser executes or interprets (script, markup, navigation). Impact: session theft, impersonation, malware delivery, data exfil. “Perfect injection resistance” = every UI variable validated, then escaped or sanitized for its sink.

Modern frameworks reduce bugs via templating and auto-escape — **gaps remain** when you leave the safe path.

| Framework gap (examples) | Risk |
|--------------------------|------|
| React `dangerouslySetInnerHTML` without sanitize | HTML becomes live DOM |
| Angular `bypassSecurityTrustAs*` | Explicitly disables protection |
| Lit `unsafeHTML` / Polymer `inner-h-t-m-l` | Same class |
| Unvalidated `javascript:` URLs | Navigation / script sinks |
| Template injection | Server-side template becomes RCE-class |
| Out-of-date plugins | Escape hatches reappear via deps |

Know where your framework protects you and where escape hatches exist. No single technique solves XSS — combine framework defaults, encoding, sanitization, and safe sinks.

### Output encoding by context

Use the **encoder for the context** where the variable is inserted. Wrong context ≈ no protection. Start with the framework’s default escaping; fill gaps with a maintained encoder library.

| Context | Safe approach |
|---------|----------------|
| **HTML body** (`<div>$var</div>`) | HTML entity encode (`& < > " '`) |
| **HTML attribute** (quoted `value`/`title`) | Attribute encode; always quote attributes; fixed attribute names |
| **JavaScript string in `<script>`** | Encoder documented for JS string + HTML script; never insert code/identifiers |
| **CSS property value only** | Allowlisted properties; never let user choose property or full declaration |
| **URL query/param** | `%HH` encode components; validate scheme (`https:`); no `javascript:` |
| **URL inside HTML attr** | URL-encode **then** attribute-encode |
| **JSON responses** | `Content-Type: application/json` (not `text/html`) |

**DOM tips:** prefer `textContent` for plain text nodes (does not parse HTML — pass original text, do not “pre-HTML-encode” then set). Prefer `addEventListener` over inline event-handler attributes with user data. `setAttribute` for ordinary text attributes assigns values — do not pre-encode for that path, and never put untrusted data into event-handler attribute names / `srcdoc`.

```text
Variable → identify sink context → apply THAT encoder
  HTML body ≠ HTML attribute ≠ JS string ≠ CSS ≠ URL
```

Illustrative HTML-body encoding (conceptual):

```html
<!-- Unsafe if $varUnsafe is raw user HTML/script -->
<div> $varUnsafe </div>

<!-- Safe pattern: framework/auto-escape or HTML-encode then insert as text -->
<div> user &lt;script&gt;…&lt;/script&gt; content as text </div>
```

### Dangerous contexts (do not put untrusted data here)

Encoding is not enough in some locations — redesign instead of “escape harder”:

| Location | Why |
|----------|-----|
| Directly inside `<script>` as code | Becomes executable |
| Inside HTML comments | Parser quirks / breakouts |
| Directly in CSS (not a property value) | CSS as attack surface |
| Defining attribute or tag **names** from user input | Structural injection |
| Callback / `eval` / `new Function` sinks | Second interpreter |
| CSS `url(...)` with unvalidated schemes | `javascript:`-class issues |

Event-handler attributes (`onclick=...`) are a dual context: HTML decode then JS interpret — HTML entity encoding alone does not make untrusted handler code safe.

### HTML sanitization

When you **must** allow a subset of HTML (rich text), use a well-maintained sanitizer with a strict allowlist of tags/attributes — encoding alone would destroy intended markup. Sanitization ≠ “strip `<script>` with regex.”

### Defense in depth (not substitutes)

| Control | Role |
|---------|------|
| Contextual encoding / sanitize | Primary |
| CSP | Limits blast radius; **not** a sole XSS fix (browser support gaps, legacy apps) |
| HTTP interceptors that “encode everything” | Often wrong context, double-encode breakage, miss DOM XSS |
| Cookie flags / SameSite | Reduce theft impact; do not stop injection |

### XXE (XML as interpreter)

Hostile XML that resolves external entities / DTDs can read local files, SSRF the network, or DoS (billion laughs). Treat untrusted XML like untrusted SQL: disable external entities and DTD resolution in the parser; prefer formats that do not need DTDs; never feed user XML to a permissive decoder. XXE often becomes SSRF ([3](./3_Access_Control_And_Authentication.md)).

### File upload (common sink class)

Upload is not “just storage” — parsers, webroots, and public retrieval expand blast radius (webshells, XXE in Office/XML, ZIP bombs, stored XSS, phishing drops).

| Control | Practice |
|---------|----------|
| Allowlist extensions | Business-needed types only; validate **after** decode; watch double ext / null byte / case / alt PHP/JSP/ASP mappings / `:` ADS |
| Do not trust `Content-Type` | Spoofable; use as UX check only |
| Magic / signature check | Defense in depth — not alone |
| Rename server-side | UUID/GUID filename; length + character limits if user names kept |
| Size limits | Upload **and** post-decompress limits |
| Authn + authZ | Only permitted users; CSRF-protect the upload POST |
| Store off webroot / other host | Map `id → file` for public access; disable per-dir config overrides (`.htaccess` / `web.config` drops) |
| Scan / CDR | AV/sandbox; disarm Office/PDF when available |
| Serve safely | `Content-Disposition: attachment`, correct type, `X-Content-Type-Options: nosniff` |

### Unsafe deserialization (A08-adjacent, RCE-class)

Native object graphs (`pickle`, Java `ObjectInputStream`, .NET `BinaryFormatter`, permissive YAML/JSON “type” fields) can instantiate gadgets → RCE, DoS, or authZ bypass. Prefer **pure data** (JSON without type gadgets). If you must deserialize: allowlist expected classes + resource limits; isolate; never unpickle untrusted bytes; prefer signed payloads you created. After-deserialize type checks are often **too late**.

### Open redirects

User-controlled redirect targets enable phishing off your origin. Allowlist destinations or map to internal route ids — do not bounce to arbitrary `https://evil.example` via `?next=`.

### Browser security headers (C8 / A02 bridge)

Headers shrink classes of bugs; they do not replace encoding or authZ. Starter set for **HTML** apps (APIs that return JSON often skip CSP/frame headers):

| Header | Typical intent |
|--------|----------------|
| `Content-Security-Policy` | Constrain script/style/frame sources (complex — dedicate a policy) |
| `Strict-Transport-Security` | Force HTTPS (configure carefully before preload) |
| `X-Content-Type-Options: nosniff` | Stop MIME sniffing |
| `Referrer-Policy` | Limit referer leakage (e.g. `strict-origin-when-cross-origin`) |
| `Permissions-Policy` | Disable unused camera/mic/geo |
| Frame isolation | Prefer CSP `frame-ancestors`; else `X-Frame-Options: DENY` |
| CORS | Specific origins; never `*` with credentials |
| Fingerprint noise | Remove/blank `Server` / `X-Powered-By` |

Turn **off** legacy `X-XSS-Protection` (can introduce bugs). Do not use HPKP / Expect-CT. Detail: [HTTP Headers Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html); program map in [7](./7_Proactive_Controls_And_Testing_Literacy.md).

### Testing injection

| Method | Role |
|--------|------|
| Code review | Spot concat into interpreters / unsafe sinks / escape hatches |
| SAST | Patterns at scale |
| DAST / fuzz | Runtime interpreters |
| Unit tests | Bound parameters; encode helpers; reject shell concat |
| XSS context tests | Same payload in body vs attribute vs JS string |
| AuthZ pairing | Injection ≠ ownership — still test IDOR on the same endpoints |
| Upload / deserialize | Extension bypass cases; ban native deserialize of untrusted input |

## Apply

1. Inventory every SQL/ORM call path; mark bound vs concatenated.  
2. Replace one concat query with parameters; add a regression test with `' OR '1'='1` style input that must return zero extra rows.  
3. Ban `Runtime.exec(string)` / shell concat in review checklist.  
4. List framework escape hatches in your stack; require review when used.  
5. For one UI field, document its output contexts (HTML / attr / JS / URL) and encoders.  
6. Ensure JSON APIs send `application/json`.  
7. If rich text exists, name the sanitizer library and allowlist.  
8. Confirm app DB role is least-privilege (no DDL / unnecessary tables).  
9. Trace one upload path against the allowlist/rename/store-off-webroot table.  
10. Ban or isolate native deserialize of untrusted input; prefer JSON DTOs.  
11. Set baseline security headers on HTML responses; verify with a header scanner.  
12. Cross-check AI/chat features that emit HTML into these sinks ([AI-Security](../AI-Security/README.md)).

### Self-check

- [ ] State prevention preference: safe API → validate → context escape  
- [ ] Explain why ORM concat is still injection  
- [ ] Narrate SQL and OS command scenario chains  
- [ ] List XSS contexts and matching defenses  
- [ ] Name unsafe framework escape hatches for your stack  
- [ ] Distinguish encoding vs HTML sanitization  
- [ ] Explain why CSP alone is insufficient  
- [ ] Know why A05 rank drop does not justify cutting injection budget  
- [ ] List file-upload controls (allowlist, rename, off-webroot, CSRF)  
- [ ] State prefer-JSON over native deserialize for untrusted data  
- [ ] Name core security headers and when CORS must not be `*`  

## Next

- [API Security Top 10:2023](./5_API_Security_Top_10_2023.md) · [ASVS verification practice](./6_ASVS_Verification_Practice.md)

## Further reading

- [A05:2025 Injection](https://owasp.org/Top10/2025/A05_2025-Injection/)
- [Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html)
- [SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
- [Query Parameterization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Query_Parameterization_Cheat_Sheet.html)
- [XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [OS Command Injection Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.html)
- [File Upload Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html)
- [Deserialization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Deserialization_Cheat_Sheet.html)
- [HTTP Headers Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
- [XML External Entity Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)
- [Unvalidated Redirects and Forwards Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html)
- [WSTG — Input validation testing](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/README)
- [AI-Security — Prompt injection / LLM10](../AI-Security/README.md)
