# Web and application surface

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

After ports say “80/443 open,” recon shifts to **what the HTTP(S) application exposes**: hosts, paths, tech, auth entry points, APIs. AppSec issue classes: [AppSec](../AppSec/README.md). Testing mindset: [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/).

**Web surface mappers** (install): [Gobuster](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Gobuster) · [ffuf](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/ffuf) · [Nikto](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nikto) · [Nuclei](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nuclei). Manual map: [Burp Suite](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive/Burp_Suite).

**Authorized / lab only** for active busting and scanners.

## Concept

### Layers

```text
IP:port (from [6](./6_Network_And_Host_Discovery.md))
    │
    ▼
TLS cert / SNI / vhost ──▶ which app?
    │
    ▼
Routes / params / APIs ──▶ attack surface
    │
    ▼
Tech fingerprint ──▶ known classes / patch priority
    │
    ▼
Auth surfaces ──▶ identity recon handoff ([8](./8_People_Org_And_Identity_Surface.md))
```

### Jobs and tools

| Job | Tool class | Tooling home |
|-----|------------|--------------|
| Path / API discovery | Dir busting, crawlers | Gobuster, ffuf |
| Vhost / DNS names | Wordlists | Gobuster dns/vhost; Amass for names |
| Fingerprint | Header/HTML/plugin cues | [WhatWeb](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/WhatWeb); Burp |
| Known misconfig / templates | Signature scanners | Nikto, Nuclei |
| Manual map | Intercepting proxy | Burp / ZAP |

## Advanced

### Virtual hosts (why IP scans lie)

One IP can host dozens of apps selected by `Host` / SNI. Recon that only hits the IP with a default host **misses** the crown jewels. Always expand names via DNS + CT + in-scope vhost wordlists.

```text
same IP
  ├─ www.example.com      → marketing
  ├─ app.example.com      → product
  └─ legacy.example.com   → forgotten admin
```

### What leaks without “exploits”

| Leak | Example | Defense |
|------|---------|---------|
| Version headers | `Server: Apache/2.4.49` | Minimal headers; still patch |
| Debug endpoints | `/actuator`, `/debug` | Disable in prod; network policy |
| Directory listing | Backup zips | Disable listing; auth |
| Swagger without auth | Full API map | Authn + non-prod only |
| Verbose 404/500 | Stack traces, paths | Generic errors |
| JS / mobile configs | API keys, internal hosts | Secret scanning; env separation |
| Source maps in prod | Near-original TypeScript | Don’t ship `.map` publicly |

### API recon specifics

| Signal | Why it matters |
|--------|----------------|
| `/openapi.json`, `/v1`, `/graphql` | Machine-readable surface |
| Different auth on docs vs API | Docs become the map |
| Undocumented twin APIs | Mobile / partner stacks |
| GraphQL introspection | Schema as recon gold |

API Top 10: [AppSec](../AppSec/README.md). OWASP API Security Project for controls.

### Crawl vs brute vs template

| Approach | Strength | Risk |
|----------|----------|------|
| Crawl from seed + sitemap | Low noise; finds linked surface | Misses unlinked paths |
| Small high-signal wordlists | Finds backups / admin | Noisy if huge |
| Template scanners (Nuclei/Nikto) | Known bad quickly | False positives; loud |

Blind multi-million wordlist runs against production without a window are often **out of RoE** and look identical to attacks.

### WSTG Information Gathering (web-shaped checklist)

Map your lab notes to WSTG IG tests: search engine recon, webserver fingerprint, app discovery, identity management entry points — then stop at recon; exploitation classes live in AppSec / Offensive web notes.

### Noise vs continuous map

Prefer:

1. Crawl from a seed + sitemap.  
2. Small high-signal wordlists.  
3. Authenticated mapping when RoE allows.  
4. Diff against last week’s map for continuous recon ([11](./11_Detect_Harden_And_Continuous_Recon.md)).

## Apply

1. Lab web app: fingerprint → crawl → small dir list → note auth URLs.  
2. Strip server version headers; re-fingerprint — note what still leaks.  
3. List five log fields (status, path, UA, volume, source) a SOC could use to spot busting.  
4. Find one API doc or OpenAPI path; decide if it should be public.

## Further reading

- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP API Security](https://owasp.org/www-project-api-security/)
- [Tooling — Gobuster](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Gobuster)
- [Tooling — ffuf](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/ffuf)
- [Tooling — Nuclei](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nuclei)
- [Tooling — WhatWeb](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/WhatWeb)
