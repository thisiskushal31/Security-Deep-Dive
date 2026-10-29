# Scanning and enumeration

[← Offensive](./README.md)

Discover what is listening and what software answers — **only on in-scope lab targets**.

## Goals

| Goal | Output |
|------|--------|
| Host discovery | Which IPs respond |
| Port / service | What is open; version hints |
| Web surface | Paths, APIs, vhosts |
| Auth surfaces | Login panels, SSO |

## Tool mapping

| Job | Tooling home |
|-----|--------------|
| Ports / scripts | [Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) |
| Fast port sweep | Masscan (lab only; easy to be rude) |
| Web dirs / fuzz | Gobuster, ffuf |
| Templates / known issues | Nuclei |
| Vuln scanners | GVM / Nikto — noisy; lab or approved windows |

Wire-level scan types: [Networks Security](https://github.com/thisiskushal31/Networks-Deep-Dive) — do not duplicate TCP handshake theory here.

## When not to scan

- Outside scope or without auth  
- Production during peak without RoE window  
- Aggressive timing that is effectively DoS  

## Next

- [Web application attacks](./5_Web_Application_Attacks.md) (issue classes)
