# Automation and SOAR boundaries

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

SOAR (Security Orchestration, Automation, and Response) means **connecting tools and playbooks** so enrichment and repeatable response steps run without a human typing the same clicks. It does not mean “auto-contain everything.” Workflow product install lives in one home: [Tooling Automation / n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n) — do not fork a second automation tree under Security. SIEM context: [3](./3_SIEM_And_Detection_Platforms.md). Triage: [12](./12_Alert_Triage_And_Case_Management.md).

## Concept

### What to automate first

| Safe / high value | Risky without gates |
|-------------------|---------------------|
| Enrich user, host, geo, ticket | Isolate production hosts |
| Open / update case with template | Disable VIP accounts |
| Pull related EDR (Endpoint Detection and Response) timeline links | Mass password resets |
| Notify on-call with context | Customer emails |
| Block known-bad IOC (indicator of compromise) in **lab** or pre-approved lists | Edge blocks without change window |

```text
Alert ──▶ enrich (auto) ──▶ human decision gate
                                │
                    ┌───────────┼───────────┐
                    ▼           ▼           ▼
                 close      escalate     contain (approved path)
```

### Human-in-the-loop

Every destructive or externally visible action needs an explicit gate: who can approve, in what tool, with what audit log. Automation without approval design creates outages that look like attacks.

## Advanced

### Playbook quality bar

| Habit | Why |
|-------|-----|
| Versioned playbooks | Know what ran during an incident |
| Idempotent steps | Retries should not double-isolate |
| Timeouts and failure paths | Hung enrich must not block triage forever |
| Secrets in a vault, not in the graph | Compromised automation = keys for everything |
| Test in lab / non-prod first | Same as any delivery pipeline |

### SOAR vs ChatOps vs scripts

| Form | Fit |
|------|-----|
| Lightweight workflow (n8n-class) | Enrich + ticket + notify |
| Full SOAR platform | Large catalogs of integrations, case-centric |
| Reviewed scripts in CI (continuous integration) | Deterministic, auditable one-shots |
| Ad-hoc shell on a laptop | Fine for lab; dangerous as the only “automation” |

Pick the smallest form that meets the control need. Product encyclopedias are out of scope; ownership of *install* stays in Tooling.

### When not to automate

- Ambiguous detections with high false-positive history  
- Legal hold / HR-sensitive cases  
- First occurrence of a novel technique  
- Any action your RoE (Rules of Engagement) / change policy forbids without a ticket  

### Link to metrics

Track: % alerts auto-enriched, mean time enrichment → human touch, automation failure rate, containment actions requiring override ([11](./11_Metrics_Purple_And_Improvement.md)).

## Apply

1. List five enrichments you still do by hand; automate one.  
2. Draw a gate for “isolate host” — who clicks approve?  
3. Move any API keys out of workflow JSON into a secrets store.  
4. Install/first-use for workflow product only via [Tooling n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n) if you need a lab.

## Further reading

- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [CISA — Incident and Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/federal-government-cybersecurity-incident-and-vulnerability-response-playbooks)
- [Tooling — n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n)
- [NIST CSF 2.0 — Respond](https://www.nist.gov/cyberframework/respond)
