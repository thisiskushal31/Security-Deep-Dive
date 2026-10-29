# Incident response

[← Defensive-Ops](./README.md)

Incidents are when detection becomes **action**. Have a written process before the page fires. On-call culture and ChatOps live in DevOps Methodologies — this page is the security IR loop.

## Lifecycle (NIST-shaped)

| Phase | Job |
|-------|-----|
| **Prepare** | Contacts, playbooks, access, retainers, tabletop |
| **Detect & analyze** | Triage severity; scope systems and data |
| **Contain** | Stop the bleeding (isolate host, revoke creds, block IOCs) |
| **Eradicate** | Remove cause (malware, backdoor, bad grant) |
| **Recover** | Restore service; watch for reinfection |
| **Lessons learned** | Fix detections, design, and runbooks |

## Severity and ownership

| Ask | Why |
|-----|-----|
| What data class is exposed? | Legal / customer notification path |
| Is attacker still active? | Containment urgency |
| Which system owner? | You cannot page “security@” forever |

Document decision makers for “take prod offline” before you need them.

## Evidence discipline

Preserve logs and disk **before** “cleaning” when forensics or legal may care ([3 Forensics](./3_Forensics.md)). Rotate compromised secrets as part of containment ([Handbook secrets](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/1_Security_Practices_And_Secrets.md)).

## Related

- Detection → [1 SIEM](./1_SIEM_And_Detection.md)
- Posture after admit → [Handbook Security/2](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/2_Compliance_And_Threat_Mitigation.md)
