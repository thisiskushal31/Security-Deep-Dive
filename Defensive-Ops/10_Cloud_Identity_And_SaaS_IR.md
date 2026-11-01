# Cloud, identity, and SaaS IR

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Modern incidents often live in **control-plane logs and identity systems**, not only on a single disk image. Cloud IR (incident response) and IdP (Identity Provider) response reuse the same lifecycle ([7](./7_Incident_Response_Lifecycle.md)) with different evidence and containment levers. Posture and architecture: [Cloud-Security](../Cloud-Security/README.md). Identity program: [Identity](../Identity/README.md).

## Concept

### What changes in the cloud

| On-prem reflex | Cloud / SaaS reflex |
|----------------|---------------------|
| Seize laptop disk | Snapshot volume / export instance metadata + logs |
| Pull network cable | Security group / NSG (network security group) / conditional access |
| Domain admin reset | Disable user, revoke sessions/tokens, rotate keys |
| Image mail server | Review mailbox rules, OAuth (Open Authorization) grants, admin audit |

```text
Alert / hunt lead
      │
      ▼
 Identity plane (SSO (single sign-on) / IdP) ──┐
 Cloud control plane (audit APIs) ─────────────┼──▶ scope
 SaaS admin / OAuth grants ────────────────────┘
      │
      ▼
 Contain (revoke, isolate, freeze) → eradicate → recover
```

### Highest-signal sources

| Source | Questions |
|--------|-----------|
| IdP sign-in / risk | Impossible travel, MFA (multi-factor authentication) fatigue, new device |
| Cloud audit (who changed what) | New keys, public storage, role assumptions |
| Mail audit | Forwarding rules, unusual sends |
| SaaS admin logs | New admins, token apps, sharing links |
| CI (continuous integration) / package logs | Stolen pipeline secrets |

## Advanced

### Containment levers that matter

| Lever | Use |
|-------|-----|
| Revoke refresh tokens / sessions | Account takeover |
| Disable API keys / rotate | Key leak |
| Remove public ACL (access control list) / lock bucket | Data exposure |
| Quarantine compute / revoke role | Compromised workload identity |
| Kill malicious OAuth app | Persistent SaaS access |

### Shared responsibility during IR

Know which plane you control. You can revoke IAM and snapshot *your* volumes; you cannot “image” a SaaS vendor’s multi-tenant disk. Use vendor IR contacts and contractual evidence processes when needed.

### Avoid tool duplication

Cloud CLI / console skills live with your cloud training and [Cloud-Security](../Cloud-Security/README.md). Workflow automation for ticket + enrich can use [Tooling n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n). Do not create a second n8n or cloud-CLI home under Defensive-Ops.

### Tie to forensics

Cloud “forensics” is mostly **export + snapshot + timeline** ([9](./9_Digital_Forensics_For_IR.md)). Practice log export paths before the incident — fifteen minutes of panic searching costs hours.

## Apply

1. Document how to revoke all sessions for a user in your IdP in under five minutes.  
2. List audit log names for your primary cloud account and retention.  
3. Tabletop: leaked access key — contain, rotate, hunt for use.  
4. Inventory SaaS apps with admin OAuth to corporate IdP; note offboarding steps.

## Further reading

- [CISA — Incident and Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/federal-government-cybersecurity-incident-and-vulnerability-response-playbooks)
- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [CISA — Cloud security resources](https://www.cisa.gov/topics/cybersecurity-best-practices/cloud-security)
- [Cloud-Security](../Cloud-Security/README.md)
- [Identity](../Identity/README.md)
