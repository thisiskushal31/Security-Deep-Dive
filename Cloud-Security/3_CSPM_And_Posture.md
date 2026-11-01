# CSPM (Cloud Security Posture Management) and continuous posture

[← Cloud-Security](./README.md)

> **Progression:** concept → advanced → apply.

Models and shared responsibility: [1](./1_Cloud_Models_And_Shared_Responsibility.md). Identity/network/data controls: [2](./2_Identity_Network_And_Data_Controls.md). This note is how you **continuously prove** the live estate matches policy — **CSPM (Cloud Security Posture Management)** and related posture disciplines — in public clouds, private clouds, hybrid, and multi-cloud — after humans, pipelines, and break-glass changes move things.

App-layer misconfiguration: [AppSec A02](../AppSec/2_OWASP_Top_10_2025_In_Practice.md). Vuln prioritization of findings: [Vulnerability-Management](../Vulnerability-Management/README.md).

## Concept

### Misconfiguration is still the default breach class

![OWASP Top 10:2025 A02 Security Misconfiguration](../Assets/Cloud-Security/OWASP_A02_Misconfiguration.png)

*Credit: OWASP Top 10:2025 — A02 Security Misconfiguration icon.*

Highly configurable platforms fail open when defaults and reviews are weak. That is true whether the platform is a hyperscaler account, an on-site private OpenStack/VMware-class cloud, or a hybrid landing zone.

| Pattern | Why it hurts |
|---------|--------------|
| Public object storage / world-open shares | Data disclosure without an exploit |
| `0.0.0.0/0` on admin ports (SSH/RDP/DB/API) | Opportunistic takeover |
| Missing org-wide / platform audit logging | Blind incident response |
| Over-broad IAM — Identity and Access Management (`*:*`, unused wildcards, forever keys) | Blast radius after any foothold |
| Root / owner credentials in daily use | No accountability |
| Debug endpoints, sample apps, default passwords | Trivial initial access |
| Security rules grown by exception only | Shadow exposure |
| Orphaned projects / subscriptions / tenants | Silent risk with no owner |

### CSPM vs IaC vs admission vs KSPM vs DSPM

| Check | Sees | When | Catches |
|-------|------|------|---------|
| **IaC (Infrastructure as Code) policy** | Planned change in Git | PR | Bad *intent* before merge |
| **Admission** | Object at apply | Deploy | Manifests that bypassed Git |
| **CSPM (Cloud Security Posture Management)** | Account / org / cloud *as-is* | Continuous | Drift, console clicks, forgotten resources |
| **KSPM (Kubernetes Security Posture Management)** | Cluster API state | Continuous | Privileged pods, weak RBAC (Role-Based Access Control), open API *live* |
| **DSPM (Data Security Posture Management)** | Where sensitive data lives and who can reach it | Continuous | Over-shared datasets, cleartext stores |

```text
  Git PR ──▶ IaC policy ──▶ merge
                              │
                              ▼
                         CI build / deploy
                              │
                              ▼
                      Admission (cluster/API)
                              │
                              ▼
           Live cloud(s) + private platforms + clusters
                ▲                    ▲              ▲
              CSPM                 KSPM           DSPM
```

CSPM is not “the AWS product.” It is the **discipline** of comparing live configuration to policy. On a private cloud you may use CIS benchmarks + custom scanners + CMDB drift; the questions are the same.

### What posture must answer (every cloud flavor)

| Domain | Questions |
|--------|-----------|
| **Identity** | Who can do what? Unused privileges? Forever keys? |
| **Compute** | Public SSH/RDP, unencrypted disks, fat instance roles, sprawl |
| **Storage** | Public buckets/blobs, open snapshots, missing versioning |
| **Data services** | Public DB endpoints, weak auth, backup exposure |
| **Platform** | Open load balancers, world-writable queues, takeover-prone DNS IAM (Identity and Access Management) |
| **Exposure** | Anything internet-reachable that should not be |
| **Logging** | Trails on, immutable retention, who can disable logging |
| **Network** | Peering, VPN, egress, flat trusts |
| **Drift** | Outside-IaC (Infrastructure as Code) resources; tag / ownership gaps |
| **Tenancy** | Org hierarchy, guardrails, orphaned accounts/projects |
| **Orchestrators** | KSPM (Kubernetes Security Posture Management) when you run clusters — a *slice*, not the whole program ([5](./5_Containers_Serverless_And_Managed_Runtimes.md)) |

## Advanced

### Control language: CIS and CCM (without a cert dump)

| Source | Use |
|--------|-----|
| **CIS (Center for Internet Security) Benchmarks** | Concrete secure configuration checks (AWS/Azure/GCP foundations, Kubernetes, and many private-stack products) |
| **CSA (Cloud Security Alliance) Cloud Controls Matrix (CCM)** | Cross-provider control catalog for mapping assurance and contracts |
| **Provider Well-Architected / security baselines** | Cloud-native phrasing of the same themes |

Map themes to **owners** (identity, network, logging, data). Do not paste hundreds of control IDs into a wiki and call it done. Prioritize internet-facing exposure, identity, and logging before niche CIS noise.

### CNAPP (Cloud-Native Application Protection Platform) and related capabilities

Vendors package overlapping capabilities. Learn the **jobs**, not the logo:

| Capability | Full form | Job |
|------------|-----------|-----|
| **CSPM** | Cloud Security Posture Management | Configuration and exposure of cloud services / accounts |
| **CWPP** | Cloud Workload Protection Platform | Workload runtime protection (VM, container, function) |
| **CIEM** | Cloud Infrastructure Entitlement Management | Entitlement and identity risk (human and machine) |
| **KSPM** | Kubernetes Security Posture Management | Cluster configuration and permission posture |
| **DSPM** | Data Security Posture Management | Where sensitive data lives and who can reach it |
| **CDR** | Cloud Detection and Response | Detection and response on control- and data-plane signals |
| **CNAPP** | Cloud-Native Application Protection Platform | Correlated view across several of the above (code → cloud → runtime) |
| **AI-SPM** | AI Security Posture Management | Posture around AI services, model endpoints, and AI-adjacent data paths ([8](./8_AI_Workloads_And_Cloud_AI_Security.md)) |

```text
Exposure + misconfig (CSPM)
        ×  identity reach (CIEM)
        ×  vuln / runtime (CWPP)
        ─────────────────────────
     attack-path / "toxic combination" priority
```

Prioritize **toxic combinations** (for example: public exposure + critical vulnerability + over-broad role) over raw finding counts. Agentless API inventory scales visibility; agents still matter where you need deep runtime telemetry. Ownership and change history decide whether remediation actually happens.

### Public vs private vs hybrid posture

| Estate | Typical blind spot |
|--------|--------------------|
| **Public** | Console drift; marketplace images; overly trusted SaaS (Software as a Service) connectors |
| **Private on-site** | Assumed “inside = safe”; weak API auth on management planes; sparse logging |
| **Outsourced private** | Provider admin access paths; unclear shared responsibility in the contract |
| **Hybrid** | Inconsistent policy across interconnect; dual break-glass; split logging |
| **Multi-cloud** | Three CSPM tools that disagree; no single risk register; duplicated IAM anti-patterns |

Org / folder / management-group **guardrails** (SCPs, Azure Policy, org policies, or private-cloud equivalents) stop individual project owners from disabling logging or opening the world.

### Landing zones and ownership

Separate **foundations** (org structure, baseline logging, network hubs, identity federation) from **workloads**. Posture tools should attach at the org root (or private-cloud equivalent), not one forgotten subscription. Every account/project needs an owner; orphaned tenancy is unpaid risk.

### Traps

| Trap | Reality |
|------|---------|
| “Managed service = no customer security work” | You still own IAM, data, exposure, and often keys |
| “CSPM green = compliant” | Exposure ≠ evidence package for auditors |
| “Only scan IaC” | Console and break-glass never hit Git |
| “Turn on every CIS rule day one” | Alert fatigue; start with P0 classes |
| “Private cloud needs no CSPM” | Misconfig and drift exist wherever APIs create resources |

## Apply

### Program checklist

1. Inventory clouds and platforms in scope (public providers, private clouds, SaaS admin planes that hold crown jewels).  
2. Enable **org-wide / platform audit logging** with retention and tamper resistance first.  
3. Deploy posture scanning at org scope; feed findings into one triage queue with owners.  
4. P0 classes: public storage, public DB/admin ports, logging disabled, root/owner misuse, critical IAM wildcards, public snapshots.  
5. Require IaC for production; treat console exceptions as time-boxed incidents.  
6. Align CIS/CCM themes to control owners — not “security owns the dashboard.”  
7. For hybrid/multi-cloud: one severity model, one exception process ([Vulnerability-Management](../Vulnerability-Management/README.md)).  
8. Re-test after every landing-zone change; guardrails regress too.

### Scenario anatomies (authorized education)

1. **Public bucket by ACL drift** — IaC said private; console share made it public; CSPM sees what PR gates missed.  
2. **Private-cloud flat management VLAN** — “internal only” API without authZ → ransomware pivots.  
3. **Child account logging off** — incident response (IR) cannot reconstruct who deleted a database.  
4. **Multi-cloud gap** — AWS CSPM clean while Azure subscription left public storage unscanned.

```text
API/console change ──▶ live state
                         │
              compare to policy / CIS-class rules
                         │
              ticket → owner → fix or accept-with-expiry
```

### Self-check

- [ ] Separate IaC (Infrastructure as Code), admission, CSPM, KSPM, and DSPM by what each sees  
- [ ] List P0 posture finding classes  
- [ ] Explain why private cloud still needs continuous posture  
- [ ] Name CIS vs CCM (Cloud Controls Matrix) roles in a program  
- [ ] Describe landing-zone vs workload split  
- [ ] State why multi-cloud needs one triage model  
- [ ] Expand CSPM, CIEM, CWPP, and CNAPP and say what CNAPP correlates  
- [ ] Explain toxic-combination prioritization with one example  

## Next

- [Securing cloud resources and workloads](./4_Securing_Cloud_Resources_And_Workloads.md)

## Further reading

- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
- [CSA Cloud Controls Matrix](https://cloudsecurityalliance.org/research/cloud-controls-matrix)
- [CSA Security Guidance](https://cloudsecurityalliance.org/research/guidance/)
- [NIST SP 800-144 — Public cloud security and privacy](https://csrc.nist.gov/pubs/sp/800/144/final)
- [OWASP Top 10:2025 A02 Security Misconfiguration](https://owasp.org/Top10/2025/A02_2025-Security_Misconfiguration/)
- [AWS Well-Architected — Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)
- [Google Cloud Architecture Framework — Security](https://cloud.google.com/architecture/framework/security)
- [Azure security baselines](https://learn.microsoft.com/en-us/security/benchmark/azure/overview)
- [DevOps-Handbook — Compliance and threat mitigation](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/2_Compliance_And_Threat_Mitigation.md)
