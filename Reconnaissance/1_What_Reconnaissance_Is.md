# What reconnaissance is

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

Reconnaissance is everything done to **learn the target’s shape** before (or without) exploitation: who they are, what they expose, what software answers, and where trust boundaries sit. Lockheed Martin Cyber Kill Chain puts it first; MITRE ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) spreads equivalent work across *Reconnaissance* and parts of *Resource Development*. Defenders run the same discovery against **themselves** under Attack Surface Management (ASM) and continuous inventory.

Literacy bridge: [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md). Authorization: [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md) · [2](./2_Scope_Authorization_And_Evidence.md).

## Concept

### Two mirrors, one surface

| Attacker / authorized tester | Defender / builder |
|------------------------------|--------------------|
| Map what can be reached | Know what *should* be reachable |
| Prioritize weak / high-value entry | Shrink and monitor that entry |
| Stay quiet when OPSEC matters | Detect quiet *and* noisy discovery |
| Turn findings into exploit plans | Turn findings into tickets and controls |

If you only study “hacker tools,” you miss half the value: **every recon technique is a detection and hardening cue**.

### What “done” looks like

A useful recon package answers:

1. **Assets** — domains, IPs, hosts, cloud accounts, SaaS tenants in scope.  
2. **Services** — ports, protocols, versions, virtual hosts.  
3. **Applications** — URLs, APIs, admin panels, auth entry points.  
4. **People / identity** — public emails, role clues, IdP (Identity Provider) hints.  
5. **Trust & tech** — CDNs (Content Delivery Networks), mail, DNS providers, third-party scripts.  
6. **Gaps** — inventory vs reality (shadow IT).

```text
Public data ──┐
              ├──▶ recon picture ──▶ prioritize ──▶ test / fix / detect
Active probes ─┘         │
                         └── evidence trail
```

### Where recon sits in larger methods

| Method | Recon-shaped stage |
|--------|--------------------|
| Cyber Kill Chain | Stage 1 — Reconnaissance |
| PTES (Penetration Testing Execution Standard) | Intelligence gathering → (feeds) threat modeling / vulnerability analysis |
| NIST SP 800-115 | Discovery techniques inside a broader assessment |
| OWASP WSTG (Web Security Testing Guide) | Information Gathering chapter before abuse tests |
| ATT&CK | Tactic TA0043 (Reconnaissance) + some prep under Resource Development |

Offensive phase stubs stay thin: [Offensive/3](../Offensive/3_OSINT.md) · [Offensive/4](../Offensive/4_Scanning_And_Enumeration.md).

### Passive vs active (preview)

| Mode | Touches target systems? | Typical early use |
|------|-------------------------|-------------------|
| **Passive** | Mostly no (third-party indexes, CT, archives) | Cheap, low noise |
| **Active** | Yes (DNS you send, port scans, HTTP) | Confirms liveness; louder |

Depth: [3](./3_Passive_Vs_Active_And_OPSEC.md).

## Advanced

### ATT&CK recon families (how it actually happens)

| Technique family (examples) | What happens in practice | Defender cue |
|-----------------------------|--------------------------|--------------|
| Search open websites / tech DBs (T1593, T1596) | Search operators; Shodan/Censys-class queries | CT/ASM diffs; brand monitoring |
| Search victim-owned websites (T1594) | Scrape careers, docs, “powered by” | Remove secrets; least public detail |
| Gather victim identity / org / network info (T1589–T1591) | People, ASN (Autonomous System Number), DNS | DMARC (Domain-based Message Authentication, Reporting and Conformance); split DNS |
| Active scanning (T1595) | Network / vuln / wordlist scans | IDS (Intrusion Detection System) / WAF (Web Application Firewall); allowlisted VA ranges |
| Phishing for information (T1598) | Often out of lab RoE — know the *mechanism* | MFA; reporting paths; IdP risk |

You do not need to memorize IDs; you need to recognize **the behavior** when it hits your logs. Full detect/harden loop: [11](./11_Detect_Harden_And_Continuous_Recon.md).

### External vs internal recon

| External | Internal (after foothold or VPN access) |
|----------|----------------------------------------|
| Public DNS, CT, edge IPs, SaaS login pages | AD (Active Directory) structure, internal subnets, SMB shares, intranet apps |
| Exposure-search indexes | Host discovery inside the LAN / VPC |
| Often pre-engagement | Often post-access — still “recon,” different RoE |

This folder emphasizes **external and internet-adjacent** recon first; internal discovery overlaps Offensive post-exploitation and Networks labs.

### Where recon stops

| Still recon | Crossed into something else |
|-------------|-----------------------------|
| Banner / version hint | Authenticated exploit / PoC against prod without RoE |
| Directory listing discovery | Dumping customer data |
| Finding a login panel | Credential stuffing live users |
| Noting an open Redis banner | Using it to pivot without authorization |

Vuln *program* scanning after discovery: [Vulnerability-Management](../Vulnerability-Management/README.md).

### Why inventory lies

| Lie | How recon catches it |
|-----|----------------------|
| “We only have three public apps” | CT (Certificate Transparency) + DNS show twelve names |
| “SSH is internal only” | Exposure search shows :22 on a forgotten Elastic IP |
| “That marketing microsite is offline” | HTTP still answers on an old CDN hostname |
| “No employee emails are public” | Collectors + Git commits disagree |
| “Staging is private” | Pre-prod SAN appears in CT before launch |

### Quality bar for a recon deliverable

| Bad deliverable | Good deliverable |
|-----------------|------------------|
| Raw tool spam | Prioritized asset list with confidence |
| “Nmap found stuff” | Open services + why they matter + evidence paths |
| Unverified emails | Pattern + verified sample + privacy note |
| No defender view | Each finding paired with detect/harden hint |

## Apply

1. Pick **one system you own** (home lab VM or cloud free tier).  
2. Write a one-page “recon picture” *without* scanning yet — DNS + vendor console only.  
3. List three things an outsider could learn from public sources alone.  
4. List three logs you would check if someone scanned you.  
5. Mark each item: still recon vs would need separate authorization.

## Further reading

- [Lockheed Martin — Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
- [MITRE ATT&CK — Reconnaissance](https://attack.mitre.org/tactics/TA0043/)
- [NIST SP 800-115 — Technical Guide to Information Security Testing](https://csrc.nist.gov/publications/detail/sp/800-115/final)
- [PTES Technical Guidelines](http://www.pentest-standard.org/index.php/PTES_Technical_Guidelines)
- [OWASP WSTG — Information Gathering](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/README)
