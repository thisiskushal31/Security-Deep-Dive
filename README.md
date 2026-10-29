# Security Deep Dive

Cybersecurity as a **program**: AppSec, identity, cloud, SOC, GRC, and PTES phases. You can open this repo knowing nothing. You do not have to finish Networks or DevOps first — those homes still own their *slice* (packets, CI gates, HLD).

Named lab software (Nmap, Burp, Wireshark) lives in [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security). Practice only in an isolated lab — never scan the public internet.

New here? Start at [Foundations](./Foundations/README.md) (21 notes, ordered concept → advanced → apply), then [Threats](./Threats/README.md). Install Nmap first in [Tooling Security / Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap).

| Folder | Owns |
|--------|------|
| [Foundations/](./Foundations/README.md) | Bedrock → program frame → technical literacy → design → operate → adversary/ops (concept → advanced → apply) |
| [Threats/](./Threats/README.md) | Actors, vectors, vulns, mitigations |
| [Identity/](./Identity/README.md) | Federation and zero trust |
| [AppSec/](./AppSec/README.md) | OWASP, API, secure SDLC |
| [Cryptography/](./Cryptography/README.md) | Practical pitfalls |
| [Cloud-Security/](./Cloud-Security/README.md) | Posture, secrets/K8s, supply chain |
| [Defensive-Ops/](./Defensive-Ops/README.md) | SIEM, IR, forensics |
| [Offensive/](./Offensive/README.md) | PTES phases (OSINT → report), lab only |
| [Threat-Modeling/](./Threat-Modeling/README.md) | Workshop/method depth (**stub**) |
| [Threat-Intelligence/](./Threat-Intelligence/README.md) | CTI analytic depth (**stub**) |
| [Vulnerability-Management/](./Vulnerability-Management/README.md) | Vuln program depth (**stub**) |
| [GRC/](./GRC/README.md) | NIST, ISO, ATT&CK |
| [Labs/](./Labs/README.md) | HTB / TryHackMe paths |

Related repos (depth lives there): [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive) (Nmap, Burp, Wireshark — install) · [Networks](https://github.com/thisiskushal31/Networks-Deep-Dive) (wire / TLS) · [DevOps Security](https://github.com/thisiskushal31/DevOps-Handbook) (CI gates) · [System Design](https://github.com/thisiskushal31/System-Design-Concepts) (`Security-Tradeoffs/`) · [Tooling OAuth](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive) (`Specs-Standards/OAuth`)
