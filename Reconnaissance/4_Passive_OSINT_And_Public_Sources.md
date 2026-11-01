# Passive OSINT and public sources

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

OSINT (Open-Source Intelligence) in recon means **structured collection from public sources**, not “stalking.” Phase stub: [Offensive/3](../Offensive/3_OSINT.md). Install collectors in Tooling: [theHarvester](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/theHarvester) · [Amass](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Amass) · [Maltego](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Maltego) · [SpiderFoot](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/SpiderFoot) · [Recon-ng](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Recon-ng). Capability group: [Tooling Reconnaissance — OSINT collectors](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance).

## Concept

### Source classes

| Class | What you learn | Defense lever |
|-------|----------------|---------------|
| **DNS** | Names, MX, TXT (SPF/DMARC), zone mistakes | Least privilege DNS; monitor changes |
| **WHOIS / RDAP** | Registrar, contacts, related domains | Privacy redaction; monitor expiry |
| **Certificate Transparency** | Every public cert → hostnames | CT monitoring; short-lived certs; no surprise SANs |
| **Search engines** | Indexed pages, operators, cached leaks | Auth walls; remove secrets from public repos |
| **Archives (Wayback)** | Old paths, forgotten panels | Decommission cleanly |
| **Code hosts / packages** | Keys, internal URLs, stack | Secret scanning; private packages |
| **Job posts / docs** | Tech stack, VPN vendors, cloud | Need-to-know in public JDs |
| **Social / professional** | People, roles, email patterns | Awareness; reduce public role maps |
| **Paste / breach indexes** | Credential exposure *awareness* | Force resets; don’t weaponize dumps |

```text
DNS + CT + WHOIS ──▶ candidate hostnames
Search + archive ──▶ paths / tech
Collectors       ──▶ emails / hosts list
        │
        ▼
Verify before treating as live ([5](./5_Internet_Exposure_And_ASM.md), [6](./6_Network_And_Host_Discovery.md))
```

### Minimal manual workflow (no fancy tool required)

1. List apex domains in scope.  
2. Pull NS / MX / TXT.  
3. Query CT for `%.domain`.  
4. Note mail / IdP vendors from MX and login pages.  
5. Only then run a collector against **your** domain.

## Advanced

### DNS habits that leak

| Record / mistake | Leak | Fix direction |
|------------------|------|---------------|
| Over-broad SPF `+all` | Mail trust abuse path | Strict SPF + DMARC reject |
| Internal hostnames in public zone | Network map | Split DNS |
| Stale MX / CNAMEs | Forgotten services | Zone hygiene tickets |
| TXT debug strings | Environment clues | Remove non-needed TXT |
| Wildcard DNS | Makes wordlist DNS “always hit” | Avoid unless required; monitor |

### Certificate Transparency in practice

Anyone can watch CT for `%.yourdomain.com`. Pre-prod names like `staging-payments.example.com` often appear **before** marketing knows the product exists. Attackers watch; so should you.

| CT finding | Action |
|------------|--------|
| Unknown SAN | Inventory ticket; confirm owner |
| Old CA still issuing | Check ACME / mismanaged accounts |
| Short-lived certs proliferating | Normal for some stacks — still inventory |

### Collector tools — treat output as hypotheses

[theHarvester](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/theHarvester) and peers automate multi-source queries. Expect:

| Issue | Mitigation |
|-------|------------|
| Stale search hits | Resolve DNS; optional HTTP check in scope |
| Duplicate / junk | Dedupe; confidence tags |
| API key / rate failures | Document source gaps; don’t invent data |
| Homoglyph lookalikes | Compare registrar + CT carefully |

Amass shines at attack-surface / subdomain enumeration when configured; Maltego / SpiderFoot for graph exploration — still verify leaves of the graph.

### Search operators (mechanism, not a crime list)

Indexes support precise queries (`site:`, `filetype:`, etc.). Defenders use them to find **their own** exposed docs. Using them to hunt random third parties for exploitation is out of scope for this curriculum.

### People-adjacent OSINT handoff

Email patterns and public profiles continue in [8](./8_People_Org_And_Identity_Surface.md). Keep PII minimization here: collect only what the engagement needs.

### WSTG alignment

OWASP WSTG Information Gathering covers search engine recon, fingerprinting, app discovery — map your checklist to those tests when the target is web-centric.

## Apply

1. For a domain you control: pull NS/MX/TXT; screenshot.  
2. Check CT for unexpected names.  
3. Run one authorized collector against **your** domain; verify top five hits manually.  
4. File one hardening ticket (remove a name, fix SPF, add CT alert).  
5. Label each collector hit: confirmed live / DNS only / false positive.

## Further reading

- [Certificate Transparency](https://certificate.transparency.dev/)
- [ICANN RDAP](https://www.icann.org/rdap)
- [MITRE ATT&CK — Gather Victim Network Information (T1590)](https://attack.mitre.org/techniques/T1590/)
- [OWASP WSTG — Information Gathering](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/README)
- [Tooling — theHarvester](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/theHarvester)
