# Security Deep Dive

Cybersecurity as a **program**: AppSec (application security), identity, cloud, SOC (Security Operations Center), GRC (Governance, Risk, and Compliance), and PTES (Penetration Testing Execution Standard) phases. You can open this repo knowing nothing. You do not have to finish Networks or DevOps first — those homes still own their *slice* (packets, CI gates, HLD).

Named lab software lives in [Tooling](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive) (**one product → one folder**; e.g. Nmap under Security/Reconnaissance, n8n under Automation). This repo links installs — it does not duplicate tool trees. Practice only in an isolated lab — never scan the public internet.

New here? Start at [Foundations](./Foundations/README.md) (21 notes, ordered concept → advanced → apply), then [Threats](./Threats/README.md) (threat landscape deep dive: classical Domain 2 + AI-era, with real cases from official reports). Install Nmap first in [Tooling Security / Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap).

| Folder | Owns |
|--------|------|
| [Foundations/](./Foundations/README.md) | Bedrock → program frame → technical literacy → design → operate → adversary/ops (concept → advanced → apply) |
| [Threats/](./Threats/README.md) | Security+ Domain 2 + AI-era literacy: actors → … → mitigations → AI-era threats |
| [Identity/](./Identity/README.md) | Assurance (IAL/AAL/FAL), OAuth/OIDC/SAML, WebAuthn, tokens, ZT, SCIM/IGA/PAM, authZ, tooling map |
| [AppSec/](./AppSec/README.md) | Secure SDLC, Top 10:2025, access control, injection, API Top 10, ASVS, proactive/WSTG |
| [AI-Security/](./AI-Security/README.md) | LLM/agent practice: defenses, RAG, agents, AIBOM/MCP, eval, shadow AI, attack mechanisms, model confidentiality / classical AML |
| [Cryptography/](./Cryptography/README.md) | Builder crypto: A04/misuse, AEAD, keys/modules, passwords, storage/secrets, TLS, PQC, HMAC/HKDF, JWT/signing patterns |
| [Cloud-Security/](./Cloud-Security/README.md) | Cloud models; IAM/firewalls/resources; posture (CSPM/CNAPP literacy); supply chain; IR; AI workloads in cloud |
| [Reconnaissance/](./Reconnaissance/README.md) | Deep recon: OSINT, exposure/ASM, network/web/identity/cloud discovery, tooling map, detect & harden |
| [Defensive-Ops/](./Defensive-Ops/README.md) | SOC/telemetry, SIEM, detection engineering, triage/SOAR bounds, hunting, IR, forensics, cloud/IdP IR, comms, exercises, metrics |
| [Offensive/](./Offensive/README.md) | PTES phases (OSINT → report), lab only — recon depth in Reconnaissance/ |
| [Threat-Modeling/](./Threat-Modeling/README.md) | Workshop process, DFD/STRIDE, PASTA, trees/LINDDUN, tooling, AI/agentic TM |
| [Threat-Intelligence/](./Threat-Intelligence/README.md) | Tradecraft, models, products, STIX/TAXII, detection handoff, malware RE for CTI |
| [Vulnerability-Management/](./Vulnerability-Management/README.md) | Program, scanning (SBOM/VEX), KEV/EPSS/SSVC/BOD, exceptions, metrics, disclosure |
| [GRC/](./GRC/README.md) | Nesting, CSF 2.0, RMF, 800-53/CIS, ISO 27001, SOC 2, NICE |
| [Labs/](./Labs/README.md) | HTB / TryHackMe paths |

Related repos (depth lives there): [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive) (Nmap, Burp, Wireshark — install) · [Networks](https://github.com/thisiskushal31/Networks-Deep-Dive) (wire / TLS) · [DevOps Security](https://github.com/thisiskushal31/DevOps-Handbook) (CI gates) · [System Design](https://github.com/thisiskushal31/System-Design-Concepts) (`Security-Tradeoffs/`) · [Tooling OAuth](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive) (`Specs-Standards/OAuth`)
