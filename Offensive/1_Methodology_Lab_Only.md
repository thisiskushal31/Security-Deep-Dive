# Methodology (lab only)

[← Offensive](./README.md)

Practice offensive security only on **systems you own or have written permission to test** — typically an isolated lab (VMs, Hack The Box-class labs, TryHackMe). Never scan or attack the public internet “to learn.”

## PTES-shaped phases

This folder follows a [PTES](http://www.pentest-standard.org/)-like flow (planning → report). Other methodologies (OWASP testing guide, NIST SP 800-115) map to the same idea: **authorize → discover → validate → report**.

| Phase | This repo |
|-------|-----------|
| Pre-engagement | [2 Planning and scope](./2_Planning_And_Scope.md) |
| Intelligence | [3 OSINT](./3_OSINT.md) |
| Threat modeling / discovery | [4 Scanning](./4_Scanning_And_Enumeration.md) |
| Vulnerability analysis / exploitation | [5](./5_Web_Application_Attacks.md)–[6](./6_Exploitation_Lab_Only.md) |
| Post-exploitation | [7](./7_Post_Exploitation.md) |
| Reporting | [9](./9_Reporting.md) |

Named tools: [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security). Purple loop with blue: [Defensive-Ops](../Defensive-Ops/README.md).

## Ethics

Unauthorized access is illegal. Lab writeups that teach **classes of issues** and **defenses** are in scope; copy-paste exploit recipes aimed at live third-party systems are not.
