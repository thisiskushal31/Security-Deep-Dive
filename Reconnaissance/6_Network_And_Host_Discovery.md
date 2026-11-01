# Network and host discovery

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

Active discovery of live hosts and listening services. Phase stub: [Offensive/4](../Offensive/4_Scanning_And_Enumeration.md). Install (one home each): [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) · [Masscan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Masscan). TCP/IP mechanics: [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive).

**Authorized / lab targets only.**

## Concept

### Pipeline

```text
Scope CIDR / host list
        │
        ▼
Host discovery (who answers ICMP/ARP/TCP ping?)
        │
        ▼
Port discovery (which ports?)
        │
        ▼
Service / version (-sV class)
        │
        ▼
Optional NSE / scripted checks (RoE!)
        │
        ▼
Export → inventory / vuln / exploit planning
```

### Tool roles (network scanners capability)

| Job | Typical tool | Notes |
|-----|--------------|-------|
| Precise, teachable scans | [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) | Default learning path |
| Very fast port sweep | [Masscan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Masscan) | Rate-limit; lab / approved windows |
| Inventory confirmation | Same scanners from VA range | Blue should know the source IPs |

Quiet-first inputs often come from [4](./4_Passive_OSINT_And_Public_Sources.md) and [5](./5_Internet_Exposure_And_ASM.md) so you are not blindly sweeping the planet.

## Advanced

### What each probe tells a defender

| Probe style | Packets / behavior | Log / sensor clue |
|-------------|--------------------|-------------------|
| ICMP echo | Ping sweep | Often blocked; silence ≠ down |
| TCP SYN scan | Half-open SYNs | Firewall SYN floods / port scans |
| TCP connect | Full handshake | Application may log connects |
| UDP scan | Sparse; many `open\|filtered` | Easy to misread |
| Version detect | Extra probes / payloads | Noisier; richer banners |
| Aggressive timing (`-T5`) | Bursty | Looks destructive |
| NSE intrusive scripts | App-level side effects | Often out of RoE without explicit OK |

### Interpreting results without lying to yourself

| Result | Meaning |
|--------|---------|
| `open` | Something accepted the handshake / probe |
| `closed` | Reachable host, port not listening (usually) |
| `filtered` | No useful reply — ACL / drop |
| Version string | **Hint** — can be spoofed or wrong |
| OS fingerprint | Probabilistic — correlate with inventory |

### Common methodology mistakes

| Mistake | Fix |
|---------|-----|
| Scan huge ranges with version detect first | Sweep ports → focus → deepen |
| Trust OS fingerprint blindly | Correlate with banners / CMDB |
| UDP “nothing open” conclusion | Longer timeouts; service-specific probes |
| One scan from one path | NAT / firewall asymmetry — agreed vantage |
| Masscan at full rate on shared links | Cap PPS; watch for collateral |

### Masscan → Nmap handoff

```text
Authorized wide range
        │
        ▼
Masscan (or similar): open ports only, controlled rate
        │
        ▼
Host:port list
        │
        ▼
Nmap -sV / scripts on the short list
```

### Relationship to vuln scanners

Network discovery feeds **coverage**. Authenticated / agent vuln scanning answers “is this patched?” — different question. [GVM](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/GVM) and program process: [Vulnerability-Management](../Vulnerability-Management/README.md).

### Output as evidence

Prefer machine-readable exports (Nmap XML / greppable) plus a short human summary. Evidence habits: [2](./2_Scope_Authorization_And_Evidence.md). Detection of the same packets: [11](./11_Detect_Harden_And_Continuous_Recon.md).

## Apply

1. Lab subnet only: host discovery → top 1000 ports → service detect on open ports ([Tooling first scan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/blob/main/Security/Reconnaissance/Nmap/2_First_Scan.md)).  
2. Save XML / greppable output with timestamp and vantage.  
3. As defender: which firewall rule or IDS signature would fire?  
4. Diff against expected inventory; ticket each surprise.  
5. Optional: compare a slow precise Nmap pass vs a rate-limited Masscan discovery on the *same* lab range.

## Further reading

- [Nmap reference guide](https://nmap.org/book/man.html)
- [MITRE ATT&CK — Active Scanning (T1595)](https://attack.mitre.org/techniques/T1595/)
- [NIST SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final)
- [Tooling — Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap)
