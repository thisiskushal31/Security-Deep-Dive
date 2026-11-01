# Contain, eradicate, and recover

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

After analysis says “this is real,” the work is **stop the bleeding**, **remove attacker control**, and **bring services back** without inviting reinfection. Lifecycle context: [7](./7_Incident_Response_Lifecycle.md). Evidence habits: [9](./9_Digital_Forensics_For_IR.md). Malware-shaped incidents need prevention *and* handling patterns that scale beyond a single host.

## Concept

### Three verbs, three risks

| Verb | Goal | Risk if done badly |
|------|------|--------------------|
| **Contain** | Limit attacker freedom now | Tip off attacker; destroy evidence; break business without authority |
| **Eradicate** | Remove malware, backdoors, bad grants | Miss persistence; leave valid credentials stolen |
| **Recover** | Restore trustworthy service | Rebuild from dirty backups; skip monitoring |

```text
Active incident
      │
      ▼
 Contain (isolate, revoke, block)
      │
      ▼
 Eradicate (remove cause, rotate secrets)
      │
      ▼
 Recover (restore, watch closely)
      │
      ▼
 Post-incident hardening ([11](./11_Metrics_Purple_And_Improvement.md))
```

### Containment menu (choose deliberately)

| Action | Typical use |
|--------|-------------|
| Network isolate host / revoke VPN | Endpoint malware, C2 (command and control) |
| Disable / reset identity; revoke sessions | Account takeover |
| Block IOC (indicator of compromise) at edge / mail | Known bad domains, hashes |
| Disable app / feature flag | Abused API or workflow |
| Take service offline | Only with agreed authority |

Containment is a **business decision** wrapped in technical steps. Pre-authorize common moves.

## Advanced

### Evidence before “cleaning”

If legal, insurance, or deep root-cause matters, image or snapshot **before** mass wipe. At minimum: preserve logs, EDR (Endpoint Detection and Response) timeline, and critical volatile notes. Then eradicate.

### Eradication checklist themes

| Area | Check |
|------|-------|
| Persistence | Services, tasks, firmware, cloud startup scripts |
| Identities | Password reset, token revoke, API keys, SSO (single sign-on) apps |
| Trust | Certificates, webhooks, CI (continuous integration) secrets |
| Mail rules | Forwarding / delegation abuse |
| Backdoors | Web shells, reverse tunnels |

Malware incidents often require **fleet** thinking: one cleaned laptop means little if the same loader hits a thousand peers — patch, block, and hunt in parallel.

### Recovery without déjà vu

| Habit | Why |
|-------|-----|
| Restore from known-good backups | Dirty backups reintroduce malware |
| Rotate secrets used on compromised systems | Stolen creds outlive reimaging |
| Heightened monitoring window | Spot reinfection early |
| Gradual reintroduction | Segment risk |

### Vulnerability overlap

When the root cause is an exploited vulnerability, IR and vuln response run together: emergency patch / mitigate, track completion, verify. Program depth: [Vulnerability-Management](../Vulnerability-Management/README.md). Playbook coordination patterns appear in public IR/vulnerability response playbooks.

### Scenario patterns (still timeless)

**Ransomware-shaped**

| Step | Focus |
|------|-------|
| Contain | Segment; disable SMB where policy allows; freeze privileged accounts |
| Eradicate | Persistence + stolen creds; do not pay as a “plan” |
| Recover | Known-good backups; rebuild identity trust; watch for reinfection |

**Business email compromise–shaped**

| Step | Focus |
|------|-------|
| Contain | Revoke sessions; mail rules; freeze finance change channels |
| Eradicate | Malicious inbox rules, OAuth (Open Authorization) apps, MFA bypass paths |
| Recover | User education is not enough — fix IdP (Identity Provider) controls |

**Cloud key leak–shaped** — see [10](./10_Cloud_Identity_And_SaaS_IR.md).

Comms during these events: [14](./14_Comms_Notification_And_External_Reporting.md). Practice: [15](./15_Tabletop_And_Purple_Exercises.md).

## Apply

1. Pre-write containment authority for: isolate endpoint, disable user, block domain.  
2. Add “preserve EDR timeline / logs before reimage” to your malware playbook.  
3. List secrets to rotate after a typical workstation compromise.  
4. Define a 7-day heightened monitoring checklist post-recovery.  
5. Draft a one-page BEC containment checklist with IdP steps.

## Further reading

- [CISA — Incident and Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/federal-government-cybersecurity-incident-and-vulnerability-response-playbooks)
- [NIST SP 800-83 Rev. 1 — Malware Incident Prevention and Handling](https://csrc.nist.gov/pubs/sp/800/83/r1/final)
- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [NIST CSF 2.0 — Respond / Recover](https://www.nist.gov/cyberframework)
