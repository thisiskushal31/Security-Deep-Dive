# Detect, harden, and continuous recon

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

Close the loop: every technique in notes **4–9** becomes a **control**, a **detection**, or an **accepted risk**. SIEM/IR depth: [Defensive-Ops](../Defensive-Ops/README.md). Posture products: [Cloud-Security/3](../Cloud-Security/3_CSPM_And_Posture.md). Program frame: [GRC](../GRC/README.md) / CSF Identify–Protect–Detect.

## Concept

### Hardening map (shrink what they can learn)

| Recon path | Hardening |
|------------|-----------|
| CT / DNS name sprawl | Naming policy; short-lived certs; no public staging |
| Exposure search hits | Remove public admin; VPN; allowlists |
| Port discovery | Ingress least privilege; close unused |
| Web busting success | No dir listing; auth on docs; clean deploys |
| Fingerprint versions | Patch; minimal headers (not your only control) |
| Email / people OSINT | DMARC enforce path; phishing-resistant MFA; reduce public GAL |
| Cloud public ACL | Block public storage; CSPM gates in CI |
| SaaS orphans | Tenant inventory; SSO + offboarding |

### Detection map (see them looking)

| Behavior | Sensor ideas |
|----------|--------------|
| Port sweeps | Firewall / IDS volume; honeypot ports |
| Dir busting | 404 spikes; distinct UAs; WAF rules |
| Unusual SSO geography | IdP risk / Conditional Access |
| CT name you didn’t request | Cert monitoring alert |
| New public bucket | Cloud trail + ASM diff |
| Collector-like scraping | WAF bot scores; rate anomalies |

```text
Continuous discovery ──▶ diff inventory
         │
         ├──▶ auto-ticket unexpected
         └──▶ detections for hostile lookalikes
```

## Advanced

### Continuous recon as a program

| Cadence | Activity |
|---------|----------|
| Daily / continuous | CT watch; cloud public ACL; IdP new apps |
| Weekly | Exposure-search diff; DNS zone diff |
| Monthly | Full authenticated inventory reconciliation |
| Per release | Pre-prod name review; robots/sitemap check |
| Per purple exercise | Disclosed scan + detection proof |

Assign an **owner** (platform security / ASM / vuln mgmt). Unowned scans become shelfware.

### Allowlists vs blind spots

If you suppress all Nmap-like traffic, you will miss **unknown** scanners. Prefer:

1. Know official VA / red-team source ranges (same [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) home every program uses — no forked scanner trees).  
2. Alert on the same signatures from elsewhere.  
3. Keep canary hosts / ports that should never be touched.

### Purple exercise (high value)

Agree ATT&CK techniques (e.g. T1595 Active Scanning, T1596 search open tech DBs). Red runs a **scoped** scan from a disclosed IP. Blue proves the alert fired and the ticket path works. Recon becomes detection engineering fuel — not a one-off demo.

### Mapping findings → tickets

| Finding class | Typical owner | Done looks like |
|---------------|---------------|-----------------|
| Unexpected public port | Network / cloud | Closed or justified + monitored |
| Public storage | Cloud app owner | Private ACL + scan clean |
| Orphan hostname in CT | Platform + app | DNS/cert removed or inventoried |
| Open API docs | AppSec + app | Auth or non-prod only |
| Weak DMARC | Messaging | Policy progressed + monitored |

### Metrics that matter

| Metric | Why |
|--------|-----|
| Unexpected internet assets open > N days | ASM health |
| Mean time to close public storage | Cloud hygiene |
| % critical apps behind SSO+MFA | Identity surface |
| Detection rate on disclosed scan tests | Purple readiness |
| CT surprises per month | Naming / pre-prod discipline |

### From recon notes back to controls

| Note | Primary close-the-loop move |
|------|----------------------------|
| [4](./4_Passive_OSINT_And_Public_Sources.md) | CT + DNS monitoring |
| [5](./5_Internet_Exposure_And_ASM.md) | Weekly exposure diff |
| [6](./6_Network_And_Host_Discovery.md) | Edge ACL + scan detections |
| [7](./7_Web_And_App_Surface.md) | WAF / app logging |
| [8](./8_People_Org_And_Identity_Surface.md) | DMARC + MFA + CA |
| [9](./9_Cloud_And_SaaS_Footprint.md) | CSPM + tenant inventory |

## Apply

1. Pick three hardening items from your own lab recon notes; implement one.  
2. Write one detection rule idea (even pseudo) for dir busting or port sweeps.  
3. Schedule a weekly CT or exposure diff for a domain you own.  
4. Run one disclosed lab scan and verify *something* logged it.  
5. Pair with [Defensive-Ops](../Defensive-Ops/README.md) when SIEM content exists for the alert path.

## Further reading

- [MITRE ATT&CK — Reconnaissance](https://attack.mitre.org/tactics/TA0043/)
- [CISA — Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
- [NIST CSF 2.0](https://www.nist.gov/cyberframework)
- [Defensive-Ops](../Defensive-Ops/README.md)
- [CISA — Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) (prioritize when exposure + KEV overlap)
