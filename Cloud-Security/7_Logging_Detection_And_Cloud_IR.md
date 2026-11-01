# Logging, detection, and cloud incident response

[← Cloud-Security](./README.md)

> **Progression:** concept → advanced → apply.

Cloud incidents fail when **telemetry was never on**, **logs were mutable**, or **nobody knew how to work with the provider**. This note covers monitoring and **IR (incident response)** literacy for public, private, hybrid, and multi-cloud estates. **SIEM (Security Information and Event Management)** product depth and classical IR process live in [Defensive-Ops](../Defensive-Ops/README.md); this folder owns the **cloud-shaped** differences.

## Concept

### What you must log (management plane first)

| Telemetry | Why it matters |
|-----------|----------------|
| **Management / control-plane audit** | Who called which API (create user, open bucket, disable trail) |
| **Identity events** | Login, MFA (multi-factor authentication) failures, federation errors, privilege elevation |
| **Network flow / DNS** (where available) | Lateral movement and exfil clues |
| **Data-plane access** (high-value stores) | Who read sensitive objects/rows |
| **Workload / app security logs** | AuthZ failures, secret access spikes (VM agents, apps, functions) |
| **Posture change events** | CSPM (Cloud Security Posture Management) finding opened/closed; guardrail exceptions |

If audit logging is off in a child account or private-cloud tenant, IR starts blind. Turning logging on is a **preparation** control, not a “during incident” task.

```text
Cloud APIs / IdP / network / workloads
              │
              ▼
     central log lake (immutable retention)
              │
              ├── detections / SIEM
              ├── IR investigation
              └── compliance evidence
```

### Detection themes that transfer across clouds

| Theme | Example signal |
|-------|----------------|
| Identity abuse | Impossible travel, disabled MFA, new access keys, role assumption spikes |
| Persistence | New federation trust, backdoor users, modified break-glass |
| Exposure change | Bucket/ACL public, security group `/0`, public snapshot |
| Defense evasion | CloudTrail/logging stopped, event selectors narrowed |
| Credential use from compute | IMDS (Instance Metadata Service)-style role use from unexpected instance/pod |
| Data movement | Unusual decrypt volume, large outbound to new destinations |

CSPM findings ([3](./3_CSPM_And_Posture.md)) are **configuration detections**; pair them with **behavioral** detections on audit logs.

### Cloud IR lifecycle (same phases, different friction)

Aligned with NIST SP 800-61-style phases and CSA cloud IR themes:

| Phase | Cloud-specific duty |
|-------|---------------------|
| **Prepare** | Logging on; retain; IR roles; CSP contacts; runbooks for disable-key / isolate-subnet / revoke-federation |
| **Detect & analyze** | Query org trails; timeline API calls; map identity → resource |
| **Contain** | Quarantine security groups; disable keys/roles; isolate accounts/projects; revoke sessions |
| **Eradicate & recover** | Rotate secrets; rebuild from known-good images/artifacts ([6](./6_Supply_Chain_And_Delivery_Integrity.md)); restore backups |
| **Post-incident** | Fix guardrails that allowed the gap; update detections; contract lessons with CSP if needed |

```text
Prepare → Detect/Analyze → Contain → Eradicate/Recover → Lessons
              ▲                         │
              └──── telemetry must already exist
```

## Advanced

### Shared-responsibility IR (who can do what)

| Actor | Typical capability |
|-------|--------------------|
| **You** | Disable your IAM principals, rotate your keys, isolate your networks, wipe your workloads, preserve *your* logs |
| **CSP** | Hypervisor/facility forensics (rarely customer-visible), abuse desk, legal process for their infra |
| **Both** | Clear severity communication; evidence export; understanding what the CSP will *not* snapshot for you |

In SaaS, containment may be **tenant admin actions** (revoke OAuth, kill sessions) more than VPC changes. In private on-site cloud, you may own facility forensics — plan staffing accordingly.

### Multi-cloud and hybrid friction

| Friction | Mitigation |
|----------|------------|
| Different log schemas | Normalize identity + resource + action fields in the SIEM |
| Split retention | Org policy: minimum retention everywhere |
| Clock skew / region fan-out | Time sync; search all regions/projects |
| On-prem + cloud dual paths | One IR commander; joint timeline |
| Provider ticket latency | Pre-open enterprise support; know severity definitions |

### Evidence and legal hold

Preserve audit logs and relevant object versions **before** aggressive cleanup. Snapshot disks / export database backups when needed under authorization. Know residency and legal process differences per region and deployment model ([1](./1_Cloud_Models_And_Shared_Responsibility.md)).

### Resilience overlaps

IR without backups and rebuild paths becomes ransom negotiation. Test restore; keep rebuild identities separate from day-to-day deploy identities; prefer promote-of-known digest over “hotfix in prod SSH.”

### Analytics and AI-assisted monitoring

Cloud telemetry volume pushes teams toward correlation, baselining, and assisted triage. Treat assistance as an **aid to analysts**, not an owner of containment:

| Practice | Why |
|----------|-----|
| Keep high-fidelity raw logs | Models and summaries are not the system of record |
| Constrain what telemetry enters prompts or third-party AI tools | Cloud trails often contain secrets, PII, and internal topology |
| Require human approval for destructive actions | Disable-key / isolate-subnet must stay dual-controlled |
| Evaluate false-positive and false-negative cost | Novel attack shapes still need detections you designed |
| Log the assistant’s suggestions and who acted | Auditability for IR and compliance |

AI workload *deployments* (endpoints, GPUs, model stores) are covered in [8](./8_AI_Workloads_And_Cloud_AI_Security.md). Application-layer LLM/agent defenses live in [AI-Security](../AI-Security/README.md).

## Apply

### Preparation checklist

1. Org-wide audit logging with tamper-resistant retention — every account/project/tenant.  
2. Alert when logging is disabled or trails change.  
3. Detections for: public exposure, new admin, key creation, federation change.  
4. Document CSP / private-cloud support contacts and evidence request paths.  
5. Tabletop: stolen access key; public bucket with PII; ransomware on hybrid file share.  
6. Break-glass accounts: MFA, monitored, rarely used.  
7. Wire SOC (Security Operations Center) playbooks to cloud API containment steps ([Defensive-Ops](../Defensive-Ops/README.md)).  
8. After incidents: add the missing guardrail to CSPM/org policy ([3](./3_CSPM_And_Posture.md)).

### Scenario anatomies (authorized education)

1. **Trail disabled in one region** — attacker deletes DBs; IR discovers gap days later.  
2. **Access key in Git** — detections show `CreateAccessKey` from unusual ASN; containment disables key and roles.  
3. **SaaS token theft** — revoke sessions and OAuth apps; VPC logs show nothing because data never touched your network.  
4. **Hybrid pivot** — on-prem malware uses VPN; cloud flow logs + IdP logs rebuild the path.

### Self-check

- [ ] List management-plane logs you would enable first on a new org  
- [ ] Name five detection themes with one example each  
- [ ] Explain CSP vs customer IR capabilities  
- [ ] Describe why SaaS incidents may leave VPC telemetry empty  
- [ ] Place logging enablement in Prepare, not Detect  
- [ ] State two controls when using AI-assisted triage on cloud logs  

## Next

- [AI workloads and cloud AI security](./8_AI_Workloads_And_Cloud_AI_Security.md)

## Related

- [Defensive-Ops](../Defensive-Ops/README.md) — SIEM / IR / forensics depth  
- [Threat-Intelligence](../Threat-Intelligence/README.md) — detection handoff  
- [Identity](../Identity/README.md) — session revoke / federation break  

## Further reading

- [NIST SP 800-61 Rev. 2 — Computer Security Incident Handling Guide](https://csrc.nist.gov/pubs/sp/800/61/r2/final)
- [CSA Security Guidance — Security Monitoring / Incident Response domains](https://cloudsecurityalliance.org/research/guidance/)
- [CISA — Cloud security technical reference](https://www.cisa.gov/cloud-security)
- [AWS — Security Incident Response Guide](https://docs.aws.amazon.com/whitepapers/latest/aws-security-incident-response-guide/welcome.html)
- [Google Cloud — Incident response](https://cloud.google.com/docs/security/incident-response)
- [Microsoft — Azure security incident management](https://learn.microsoft.com/en-us/security/operations/incident-response-overview)
- [OWASP Top 10:2025 A09 Logging and Alerting Failures](https://owasp.org/Top10/2025/A09_2025-Security_Logging_and_Alerting_Failures/)
