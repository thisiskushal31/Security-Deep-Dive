# Reconnaissance

[← README](../README.md)

Deep dive on **how discovery works**: what attackers and authorized testers learn before exploitation, which signals they chase, which tools belong to which job, and how defenders shrink and monitor that same surface. Kill Chain literacy: [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md). Ethics / lab: [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md). PTES (Penetration Testing Execution Standard) phase stubs: [Offensive/3](../Offensive/3_OSINT.md) · [Offensive/4](../Offensive/4_Scanning_And_Enumeration.md) — **depth lives here**.

**Install and first-use** (capability **labels** only): [Tooling Security / Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance). One product → one Tooling folder (e.g. Nmap); do not create `Shared-Tools/` or re-home installs here. Workflow tools like n8n stay in Tooling [Automation](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n), not under Security.

This folder is **not** an unauthorized attack cookbook. It explains mechanisms fully so you can **defend**, run **authorized** assessments, and practice in **labs**. Active probes: systems you own or have written RoE (Rules of Engagement) for.

Each note: **concept → advanced → apply**. Official Further reading only. Prefer diagrams in-note; optional Assets later.

```text
1 What recon is (attacker + defender)
  → 2 Scope, RoE, evidence (practical, not bureaucracy)
    → 3 Passive vs active; OPSEC both sides
      → 4 Passive OSINT & public sources
        → 5 Internet exposure & ASM
          → 6 Network & host discovery
            → 7 Web & app surface
              → 8 People, org & identity surface
                → 9 Cloud & SaaS footprint
                  → 10 Tooling map (→ Tooling repo)
                    → 11 Detect, harden, continuous recon
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [What reconnaissance is](./1_What_Reconnaissance_Is.md) |
| 2 | [Scope, authorization, and evidence](./2_Scope_Authorization_And_Evidence.md) |
| 3 | [Passive vs active and OPSEC](./3_Passive_Vs_Active_And_OPSEC.md) |
| 4 | [Passive OSINT and public sources](./4_Passive_OSINT_And_Public_Sources.md) |
| 5 | [Internet exposure and ASM](./5_Internet_Exposure_And_ASM.md) |
| 6 | [Network and host discovery](./6_Network_And_Host_Discovery.md) |
| 7 | [Web and application surface](./7_Web_And_App_Surface.md) |
| 8 | [People, org, and identity surface](./8_People_Org_And_Identity_Surface.md) |
| 9 | [Cloud and SaaS footprint](./9_Cloud_And_SaaS_Footprint.md) |
| 10 | [Tooling map](./10_Tooling_Map.md) |
| 11 | [Detect, harden, and continuous recon](./11_Detect_Harden_And_Continuous_Recon.md) |

## Learning path

Recommended: `1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11`.

Install [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) early if you want hands-on with **6**; read **2** before any active scan.

## Topic map

| Area | Home |
|------|------|
| Goals; Kill Chain / ATT&CK recon; blue mirror | 1 |
| Scope fields; evidence; what “no red tape” still requires | 2 |
| Passive vs active; tester OPSEC; defender OPSEC | 3 |
| DNS, WHOIS, CT, search, collectors | 4 |
| Shodan/Censys-class; self-ASM | 5 |
| Host/port/service; Nmap/Masscan roles | 6 |
| Paths, vhosts, fingerprint, API discovery | 7 |
| Email, employees, phishing-relevant public data | 8 |
| Cloud accounts, buckets, SaaS tenants | 9 |
| Capability → Tooling routing | 10 |
| Detections, hardening, continuous programs | 11 |

## Related homes (do not duplicate)

| Topic | Home |
|-------|------|
| Authorization bedrock | [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md) |
| Kill Chain / ATT&CK literacy | [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) |
| PTES phase order | [Offensive](../Offensive/README.md) |
| Tool install + capability groups | [Tooling Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance) |
| Vuln scanning *program* | [Vulnerability-Management](../Vulnerability-Management/README.md) |
| Cloud posture products | [Cloud-Security](../Cloud-Security/README.md) |
| SIEM / IR consumption of scan noise | [Defensive-Ops](../Defensive-Ops/README.md) |
| Wire / TCP scan theory | [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive) |
