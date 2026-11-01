# Tooling map

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

Route **methodology here** → **install in Tooling**. Never copy a tool folder into Security-Deep-Dive. Never create a second home in Tooling for a product that already exists (same rule as n8n → [Automation/n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n), not under Security).

Home: [Tooling Security / Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance).

## Concept

### Two homes

| Home | Owns |
|------|------|
| **This folder (Reconnaissance)** | How recon works; order; defense; evidence |
| [Tooling Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance) | Install, first commands, lab gotchas; **capability table** (labels only) |

```text
Methodology (here) ──▶ capability label (Tooling README) ──▶ existing tool folder (install)
         │
         └──▶ Offensive phase stubs (thin)
```

### Capability → existing Tooling folders → notes

| Capability (label in Tooling) | Existing product folders | Primary recon notes |
|-------------------------------|--------------------------|---------------------|
| **Network scanners** | [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap), [Masscan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Masscan) | [6](./6_Network_And_Host_Discovery.md) |
| **OSINT collectors** | theHarvester, Amass, **subfinder**, Maltego, SpiderFoot, Recon-ng — [Tooling Recon](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance) | [4](./4_Passive_OSINT_And_Public_Sources.md) · [8](./8_People_Org_And_Identity_Surface.md) |
| **Web surface mappers** | Gobuster, ffuf, **feroxbuster**, Nikto, Nuclei, WhatWeb, **httpx** — same Tooling folder | [7](./7_Web_And_App_Surface.md) |
| **Exposure search** | Shodan **or** Censys | [5](./5_Internet_Exposure_And_ASM.md) |
| **Vuln overlap** | [GVM](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/GVM), Nuclei | [Vulnerability-Management](../Vulnerability-Management/README.md) |

### Also used in recon (other Tooling areas — link, don’t fork)

| Job | Existing Tooling home |
|-----|----------------------|
| Intercept / manual web map | [Burp Suite](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive/Burp_Suite) |
| Packet truth | [Wireshark](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wireshark) |
| Workflow automation (playbooks) | [n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n) under **Automation**, not Security |

## Advanced

### Anti-duplication rules

| Do | Don’t |
|----|-------|
| Search Tooling; link the existing folder | Create `Shared-Tools/` or a second Nmap/n8n tree |
| Put install gaps **in Tooling** then link | Paste `brew install` manuals into Security notes |
| Use capability **labels** in Tooling’s README | Clone every CLI under a new capability directory |
| One Nmap folder for recon + vuln + blue | Fork “Nmap-for-blue” / “Nmap-for-recon” |

### Suggested lab kit order

1. [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap)  
2. Browser + [Burp](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive/Burp_Suite)  
3. [theHarvester](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/theHarvester)  
4. [Gobuster](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Gobuster) or [ffuf](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/ffuf)  
5. [Shodan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Shodan) (**your** assets)  
6. Optional: Masscan, Nikto, Nuclei, Amass, GVM  

## Apply

1. Open [Tooling Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance); confirm the tool you need already has a folder.  
2. Install via that folder only; complete Tooling’s first lab.  
3. Quiet-first path on a lab target: [4](./4_Passive_OSINT_And_Public_Sources.md) → [6](./6_Network_And_Host_Discovery.md) → [7](./7_Web_And_App_Surface.md).

## Further reading

- [Tooling Security / Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance)
- [Tooling Automation / n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n) (workflow — not a Security tool fork)
- [Offensive](../Offensive/README.md)
