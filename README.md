# Security Deep Dive

Cybersecurity as a **program**: AppSec, identity, cloud, SOC, GRC, and PTES phases. You can open this repo knowing nothing. You do not have to finish Networks or DevOps first — those homes still own their *slice* (packets, CI gates, HLD).

Named lab software (Nmap, Burp, Wireshark) lives in [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security). Practice only in an isolated lab — never scan the public internet.

New here? Start at [Foundations](./Foundations/README.md) (21 notes, ordered concept → advanced → apply), then [Threats](./Threats/README.md) (threat landscape deep dive: classical Domain 2 + AI-era, with real cases from official reports). Install Nmap first in [Tooling Security / Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap).

| Folder | Owns |
|--------|------|
| [Foundations/](./Foundations/README.md) | Bedrock → program frame → technical literacy → design → operate → adversary/ops (concept → advanced → apply) |
| [Threats/](./Threats/README.md) | Security+ Domain 2 + AI-era literacy: actors → … → mitigations → AI-era threats |
| [Identity/](./Identity/README.md) | Federation and zero trust |
| [AppSec/](./AppSec/README.md) | OWASP, API, secure SDLC |
| [AI-Security/](./AI-Security/README.md) | LLM/agent practice: defenses, RAG, agents, AIBOM/MCP, eval, shadow AI, attack mechanisms for defenders |
| [Cryptography/](./Cryptography/README.md) | Practical pitfalls |
| [Cloud-Security/](./Cloud-Security/README.md) | Posture, secrets/K8s, supply chain |
| [Defensive-Ops/](./Defensive-Ops/README.md) | SIEM, IR, forensics |
| [Offensive/](./Offensive/README.md) | PTES phases (OSINT → report), lab only |
| [Threat-Modeling/](./Threat-Modeling/README.md) | Workshop process, DFD/STRIDE, PASTA, trees/LINDDUN, tooling, AI/agentic TM |
| [Threat-Intelligence/](./Threat-Intelligence/README.md) | Tradecraft, models, products, STIX/TAXII, detection handoff, malware RE for CTI |
| [Vulnerability-Management/](./Vulnerability-Management/README.md) | Program, scanning (SBOM/VEX), KEV/EPSS/SSVC/BOD, exceptions, metrics, disclosure |
| [GRC/](./GRC/README.md) | Nesting, CSF 2.0, RMF, 800-53/CIS, ISO 27001, SOC 2, NICE |
| [Labs/](./Labs/README.md) | HTB / TryHackMe paths |

Related repos (depth lives there): [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive) (Nmap, Burp, Wireshark — install) · [Networks](https://github.com/thisiskushal31/Networks-Deep-Dive) (wire / TLS) · [DevOps Security](https://github.com/thisiskushal31/DevOps-Handbook) (CI gates) · [System Design](https://github.com/thisiskushal31/System-Design-Concepts) (`Security-Tradeoffs/`) · [Tooling OAuth](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive) (`Specs-Standards/OAuth`)
