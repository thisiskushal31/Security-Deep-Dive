# Scope, authorization, and evidence

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

“No red tape” means: skip theater, keep the **minimum that keeps you legal and useful**, then teach mechanisms honestly. It does **not** mean skip written targets. Bedrock: [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

## Concept

### What you actually need before active recon

| Field | Plain meaning |
|-------|----------------|
| **Targets** | Exact domains, IP ranges, cloud account IDs, apps — not “everything related to Acme” |
| **Out of scope** | Third-party SaaS, shared-hosting neighbors, DoS (denial of service), social engineering — list them |
| **Window** | When noisy scans are allowed |
| **Allowed techniques** | Passive only? Port scan? Dir bust? Cred stuffing? Phishing? |
| **Contacts** | Who to call if you knock something over |
| **Data handling** | Where screenshots / dumps live; retention; who can read them |

Bug bounty programs publish this as program rules. Corporate tests put it in RoE (Rules of Engagement). Home labs: **you** are the authorizer — still write the range so you do not scan the wrong Wi-Fi.

### Evidence is not bureaucracy

You keep evidence so findings are **reproducible** and so defenders can **fix** the same path.

| Artifact | Why |
|----------|-----|
| Command + timestamp + target + vantage IP | Replay / dispute resolution |
| Raw tool output (XML/JSON/txt) | Diff later; prove port was open *then* |
| Screenshots of critical UI | Non-technical stakeholders |
| Notes of false positives | Stops repeat rabbit holes |
| Scope version / RoE ID | Ties work to authorization |

```text
Authorize → record scope → collect → save raw → summarize → hand off
```

### Mapping to program types

| Context | Authorization shape |
|---------|---------------------|
| Lab (HTB, THM, your VPC) | Platform ToS + your subnet list |
| Bug bounty | Public program scope page |
| Internal purple team | Ticket + change window + disclosed source IPs |
| External pentest | Signed RoE + NDA (non-disclosure agreement) |

## Advanced

### Scope creep (how people get burned)

| Creep | What goes wrong |
|-------|-----------------|
| “Related” subdomain not listed | Legal / client dispute |
| Cloud account shared with another brand | You scanned a neighbor |
| CDN (Content Delivery Network) shared IP | Hitting other customers’ vhosts |
| Employee home IP in DHCP range | Personal devices out of RoE |
| Wildcard `*.example.com` without exclusions | Marketing microsites, partner portals, parked names |

When DNS or CT (Certificate Transparency) reveals a new name: **pause**, confirm with the authorizer, then continue. Capture the new name in an amended scope note.

### Passive collection still has constraints

| Constraint | Why it matters |
|------------|----------------|
| Site ToS / robots | Some automated scraping is prohibited |
| Personal data minimization | Don’t hoard résumés or private emails “for later” |
| Paste-site / breach corpora | Defensive awareness ≠ weaponizing dumps against people |
| Rate limits / API keys | Abuse gets keys revoked and looks hostile |
| Export controls / local law | Some jurisdictions treat unauthorized access broadly |

### Safe evidence hygiene

| Do | Don’t |
|----|-------|
| Store under engagement ID | Paste customer banners into public Discord |
| Redact secrets in shared decks | Commit API keys from recon into Git |
| Hash or ticket sensitive dumps | Keep “souvenir” customer PII forever |
| Label confidence (confirmed / suspected) | Present ghosts as confirmed live assets |

### Vantage point matters

| Vantage | What you see |
|---------|----------------|
| Home broadband | Consumer NAT path; often blocked by enterprise edge |
| Disclosed cloud VPS | Agreed “attacker” path; blue can allowlist |
| Corporate VPN | Internal recon — different scope entirely |
| Vendor ASM index | Their crawl path, not yours |

Record **from where** you scanned. Two teams comparing Nmap results without vantage notes argue forever.

## Apply

1. Write a **five-line RoE** for a single lab VM (targets, out-of-scope, techniques, window, contact = you).  
2. Run one authorized host discovery; save stdout to a dated file with vantage IP noted.  
3. Write three sentences a defender could use to recreate your scan from the evidence alone.  
4. Add one “discovered but not yet authorized” hostname and practice the pause-and-ask habit.

## Further reading

- [Foundations/3 — Ethics, authorization, and lab rules](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md)
- [NIST SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final)
- [PTES — Pre-engagement](http://www.pentest-standard.org/index.php/Pre-engagement)
- [OWASP — Testing Guide (ethics / rules framing in project docs)](https://owasp.org/www-project-web-security-testing-guide/)
