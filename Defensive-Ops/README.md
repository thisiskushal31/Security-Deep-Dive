# Defensive-Ops

[← README](../README.md)

Detect, investigate, respond, and improve. This folder is the **security operations** path: telemetry and SIEM (Security Information and Event Management) habits, detection engineering against ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge), alert triage and case management, automation boundaries, threat hunting, network and endpoint detection roles, incident response under current NIST (National Institute of Standards and Technology) guidance, forensics in support of IR (incident response), cloud and identity incident patterns, communications and notification, exercises, and metrics that change behavior.

Named capture, NSM (Network Security Monitoring), endpoint query, and forensics tools live in [Tooling Security / Defensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive). Workflow automation (alert → ticket → enrich) lives in [Tooling Automation / n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n) — one home, not duplicated here. Packet depth: [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive). CTI (Cyber Threat Intelligence) products and STIX (Structured Threat Information Expression) handoff: [Threat-Intelligence](../Threat-Intelligence/README.md). Recon signals that feed detections: [Reconnaissance/11](../Reconnaissance/11_Detect_Harden_And_Continuous_Recon.md).

## Purple team (no separate folder)

**Purple** work is offense informing defense: agree on ATT&CK techniques, run **authorized** lab exercises, and improve detections. Use [Offensive](../Offensive/README.md) for PTES (Penetration Testing Execution Standard)-shaped phases and this folder for SIEM / IR / forensics outcomes. Exercise design: [15](./15_Tabletop_And_Purple_Exercises.md).

Each note follows **concept → advanced → apply**. Official Further reading only. Adversarial validation of detections stays **authorized and isolated** — [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

```text
1 Defensive ops & SOC shape
  → 2 Telemetry & log management
    → 3 SIEM & detection platforms
      → 4 Detection engineering & ATT&CK
        → 12 Alert triage & case management
          → 13 Automation & SOAR boundaries
            → 6 Network & endpoint detection
              → 7 Incident response lifecycle
                → 8 Contain, eradicate, recover
                  → 9 Digital forensics for IR
                    → 14 Comms, notification & reporting
                      → 5 Threat hunting
                        → 10 Cloud, identity & SaaS IR
                          → 15 Tabletop & purple exercises
                            → 11 Metrics & improvement
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [Defensive ops and SOC shape](./1_Defensive_Ops_And_SOC_Shape.md) |
| 2 | [Telemetry and log management](./2_Telemetry_And_Log_Management.md) |
| 3 | [SIEM and detection platforms](./3_SIEM_And_Detection_Platforms.md) |
| 4 | [Detection engineering and ATT&CK](./4_Detection_Engineering_And_ATTCK.md) |
| 5 | [Threat hunting](./5_Threat_Hunting.md) |
| 6 | [Network and endpoint detection](./6_Network_And_Endpoint_Detection.md) |
| 7 | [Incident response lifecycle](./7_Incident_Response_Lifecycle.md) |
| 8 | [Contain, eradicate, and recover](./8_Contain_Eradicate_Recover.md) |
| 9 | [Digital forensics for IR](./9_Digital_Forensics_For_IR.md) |
| 10 | [Cloud, identity, and SaaS IR](./10_Cloud_Identity_And_SaaS_IR.md) |
| 11 | [Metrics, purple, and improvement](./11_Metrics_Purple_And_Improvement.md) |
| 12 | [Alert triage and case management](./12_Alert_Triage_And_Case_Management.md) |
| 13 | [Automation and SOAR boundaries](./13_Automation_And_SOAR_Boundaries.md) |
| 14 | [Comms, notification, and external reporting](./14_Comms_Notification_And_External_Reporting.md) |
| 15 | [Tabletop and purple exercises](./15_Tabletop_And_Purple_Exercises.md) |

## Learning path

Recommended: `1 → 2 → 3 → 4 → 12 → 13 → 6 → 7 → 8 → 9 → 14 → 5 → 10 → 15 → 11`.

Triage/automation sit after detection design. Hunt after you can respond. Exercises and metrics close the loop.

## Topic map

| Area | Home |
|------|------|
| SOC / CSIRT (Computer Security Incident Response Team) mission and services | 1 |
| What to log, retain, protect; canaries | 2 |
| SIEM jobs; UEBA (User and Entity Behavior Analytics) baselines | 3 |
| Coverage, fidelity, detection lifecycle | 4 |
| Hypothesis-driven hunting | 5 |
| IDPS (Intrusion Detection and Prevention System), EDR (Endpoint Detection and Response), NSM, mail/DNS | 6 |
| IR as risk management (CSF (Cybersecurity Framework) 2.0–shaped); crisis mode | 7 |
| Stop bleeding, remove cause, restore; ransomware/BEC patterns | 8 |
| Evidence for IR decisions | 9 |
| Cloud audit, IdP (Identity Provider), SaaS tenants | 10 |
| MTTD/MTTR honesty; analyst health | 11 |
| Alert triage, tiers, case quality | 12 |
| SOAR / automation gates (n8n home in Tooling) | 13 |
| Internal/external notification | 14 |
| Tabletop and purple design | 15 |

## Related homes (do not duplicate)

| Topic | Home |
|-------|------|
| Authorization / lab rules | [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md) |
| Kill Chain / ATT&CK overview | [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) |
| ATT&CK nesting with frameworks | [GRC](../GRC/README.md) |
| Tool installs (Wazuh, TheHive, CyberChef, Wireshark/tcpdump, Zeek/Suricata/Falco, osquery/Velociraptor, Volatility, Autopsy, YARA/ClamAV) | [Tooling Defensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive) |
| Workflow automation | [Tooling n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n) |
| CI SAST / secrets (SonarQube **or** SonarCloud, Semgrep, CodeQL, Gitleaks **or** TruffleHog) | [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security) |
| Cloud posture | [Cloud-Security](../Cloud-Security/README.md) |
| Identity / SSO (single sign-on) | [Identity](../Identity/README.md) |
| Vuln response program | [Vulnerability-Management](../Vulnerability-Management/README.md) |
