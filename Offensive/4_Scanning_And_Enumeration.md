# Scanning and enumeration

[← Offensive](./README.md)

Discover what is listening and what software answers — **only on in-scope lab targets**.

> **Depth lives in** [Reconnaissance/6](../Reconnaissance/6_Network_And_Host_Discovery.md) · [Reconnaissance/7](../Reconnaissance/7_Web_And_App_Surface.md). Network scanners / web mappers: [Tooling Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance). This note is the PTES **phase** pointer.

## Goals

| Goal | Output |
|------|--------|
| Host discovery | Which IPs respond |
| Port / service | What is open; version hints |
| Web surface | Paths, APIs, vhosts |
| Auth surfaces | Login panels, SSO (single sign-on) |

## Tool mapping

| Job | Home |
|-----|------|
| Ports / scripts | [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) |
| Fast port sweep | [Masscan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Masscan) (lab only; easy to be rude) |
| Web dirs / fuzz | [Gobuster](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Gobuster) · [ffuf](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/ffuf) |
| Templates / known issues | [Nuclei](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nuclei) · [Nikto](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nikto) |
| Vuln scanners | [GVM](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/GVM) — noisy; lab or approved windows |

Wire-level scan types: [Networks Security](https://github.com/thisiskushal31/Networks-Deep-Dive) — do not duplicate TCP handshake theory here.

## When not to scan

- Outside scope or without auth  
- Production during peak without RoE window  
- Aggressive timing that is effectively DoS  

## Next

- [Web application attacks](./5_Web_Application_Attacks.md) (issue classes)
- Defense loop: [Reconnaissance/11](../Reconnaissance/11_Detect_Harden_And_Continuous_Recon.md)
