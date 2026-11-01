# Internet exposure and ASM

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

Attack Surface Management (ASM) is continuous **self-recon**: discover what the internet can already see about you, then close or accept it. Tooling: [Shodan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Shodan) under **exposure search** in [Tooling Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance). Cloud posture products: [Cloud-Security/3](../Cloud-Security/3_CSPM_And_Posture.md).

## Concept

### Why indexes exist

Vendors crawl public address space (and more), store banners, and sell **search**. You query an index — you are not inventing global scanning from your laptop (and you should not try to).

| You query for… | You should act on… |
|----------------|--------------------|
| Your domains, org names, ASNs | Unexpected products / ports |
| Your cert subjects | Names not in inventory |
| Known bad defaults (open DBs) | **Your** hits only |

```text
Internet ──▶ vendor crawlers ──▶ index
                                  │
                         your ASM queries
                                  │
                                  ▼
                         triage → fix / accept → recheck
```

### Exposure categories that show up first

| Category | Example | Typical fix |
|----------|---------|-------------|
| Admin panels | `/admin`, vendor UI on :8443 | VPN / SSO / IP allowlist |
| Remote access | RDP, SSH, VPN concentrators | MFA; avoid direct internet if possible |
| Data stores | Redis, Mongo, Elasticsearch without auth | Bind internal; auth; kill public NSG |
| Dev leftovers | Jenkins, Grafana, swagger unauthenticated | Auth + network policy |
| Mail / file | Exposed SMB, misconfigured FTP | Remove from edge |
| IoT / printers | Device panels on public IPs | Segment; don’t publish |

## Advanced

### Attribution pitfalls

| Pitfall | What to do |
|---------|------------|
| Shared CDN IP | Confirm Host header / cert SAN ownership |
| Cloud reseller / MSP ranges | Check account tags, not just ASN |
| Parked / sinkhole domains | Don’t page onlookers |
| Historical banners | Re-validate liveness before incident |
| Lookalike orgs in same index | Exact domain / ASN match |

### ASM vs vulnerability scanning

| ASM | Vuln scanning |
|-----|----------------|
| “What exists on the internet that is ours?” | “Is this known-bad / misconfigured?” |
| Discovery + ownership | Coverage + CVE (Common Vulnerabilities and Exposures) / checks |
| Feeds inventory | Feeds risk / patch |

Both belong in a program; neither replaces the other. Vuln program: [Vulnerability-Management](../Vulnerability-Management/README.md). Network confirm: [6](./6_Network_And_Host_Discovery.md).

### Continuous loop

1. Discover (CT + DNS + exposure search + cloud inventory).  
2. Own (confirm asset tag / DNS / billing).  
3. Classify (prod / staging / orphan).  
4. Control (close, authenticate, monitor).  
5. Detect regressions (weekly diff of query results).

### Query craft (owned assets only)

| Idea | Purpose |
|------|---------|
| Org / domain filters | Reduce noise |
| Product + version | Patch priority candidates |
| Port / service facets | Unexpected remote access |
| SSL / cert fields | Name discovery adjacent to CT |

Never treat “interesting random host on the internet” as a practice target.

### Program ownership

| Role | Responsibility |
|------|----------------|
| Platform / cloud security | Public ACL / SG truth |
| Vulnerability management | Scan coverage after discovery |
| SOC (Security Operations Center) | Alerts on new exposures / hostile scans |
| App owners | Fix their panels and leftovers |

Unowned ASM becomes a dashboard nobody watches — [11](./11_Detect_Harden_And_Continuous_Recon.md).

## Apply

1. Query an exposure engine **only** for assets you own (after [Tooling Shodan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Shodan) setup if needed).  
2. Build a table: hostname / port / product / expected? / ticket.  
3. Close or document the top unexpected hit.  
4. Schedule a weekly diff of the same query.

## Further reading

- [CISA — Directives and exposure-reduction guidance](https://www.cisa.gov/news-events/directives)
- [MITRE ATT&CK — Search Open Technical Databases (T1596)](https://attack.mitre.org/techniques/T1596/)
- [Tooling — Shodan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Shodan)
- [Cloud-Security — posture](../Cloud-Security/3_CSPM_And_Posture.md)
