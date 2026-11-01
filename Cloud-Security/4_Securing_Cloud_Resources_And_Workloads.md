# Securing cloud resources and workloads

[← Cloud-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to secure cloud resources and workloads — managed or self-operated — across public, private, hybrid, and multi-cloud estates. Identity, firewalls, and keys: [2](./2_Identity_Network_And_Data_Controls.md). Models and shared responsibility: [1](./1_Cloud_Models_And_Shared_Responsibility.md). Containers, serverless, and managed app platforms: [5](./5_Containers_Serverless_And_Managed_Runtimes.md).

## Concept

### One security question for every object

Whatever the console calls it, every cloud object answers the same questions:

```text
What is it?          (resource / workload class)
Who can admin it?    (management-plane IAM)
Who can use it?      (data-plane / runtime identity)
What can reach it?   (network + firewall layers)
What data does it hold? (classification + encryption + keys)
How is it changed?   (IaC / console / pipeline)
How would we know?   (logging + posture)
Managed or not?      (who patches the engine vs who sets exposure)
```

Answer those for each object in scope before trusting a dashboard green check.

### Cloud surfaces

| Surface | Includes (illustrative — any provider) | Secure by |
|---------|----------------------------------------|-----------|
| **Management / control plane** | Console, APIs, org/account hierarchy, IaC runners that hold cloud creds | MFA, least privilege, org guardrails, audit logs ([2](./2_Identity_Network_And_Data_Controls.md), [7](./7_Logging_Detection_And_Cloud_IR.md)) |
| **Network fabric** | VPC/VNet, subnets, route tables, peering, VPN/Interconnect, DNS, DHCP options | Zoning, private by default ([2](./2_Identity_Network_And_Data_Controls.md)) |
| **Firewall & edge protections** | Security groups/NSGs, NACLs, host FW, NGFW appliances, WAF, DDoS | Layered allow-lists ([2](./2_Identity_Network_And_Data_Controls.md)) |
| **Compute (unmanaged guest)** | VMs, bare metal, autoscaling groups, batch nodes you patch | Image baseline, patch, host FW, instance identity, no public admin |
| **Compute (managed / abstracted)** | FaaS, managed containers, platform app services | Execution identity, triggers, private networking ([5](./5_Containers_Serverless_And_Managed_Runtimes.md)) |
| **Orchestrated compute** | Kubernetes and cousins | Policy + RBAC + workload identity — still compute ([5](./5_Containers_Serverless_And_Managed_Runtimes.md)) |
| **Block / file storage** | Disks, volumes, NFS/SMB | Encryption, snapshot policy, attach rights |
| **Object storage** | Buckets / blobs | Block public access, IAM, versioning, access logs |
| **Data platforms** | SQL/NoSQL, warehouses, search, caches — managed *or* self-hosted on VMs | Exposure, auth, encryption, backups |
| **Messaging & integration** | Queues, topics, buses, streams, workflow engines | Auth publish/subscribe, encrypt payloads |
| **Delivery & edge** | Load balancers, API gateways, CDN, DNS zones | TLS policy, origin private, DNS IAM |
| **Certificates & secrets** | ACME/private CA, secret managers, parameter stores | Short life, least read, no Git/userdata |
| **Identity services** | Cloud IAM, directories, federation trusts | Treat as crown jewels |
| **AI / analytics / other PaaS** | Managed notebooks, model endpoints, ETL | Same: identity, data, network, supply chain |
| **SaaS admin planes** | M365, Salesforce-class, ITSM, Git hosts as SaaS | SSO, sharing, OAuth apps, admin roles |
| **CI/CD & registries** | Build systems, artifact/container registries | Dual control, sign/verify ([6](./6_Supply_Chain_And_Delivery_Integrity.md)) |

```text
                    management plane (API / console / org policy)
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
   network + firewalls   every workload/resource   keys + logs
   (zones, SG, WAF…)     (compute → SaaS list)     (KMS, trails)
```

### Managed vs unmanaged — same checklist, different hands

| Control | Unmanaged example (you run Postgres on a VM) | Managed example (provider Postgres service) |
|---------|-----------------------------------------------|---------------------------------------------|
| Patch engine/OS | **You** | Provider (you still track versions/features) |
| Host firewall | **You** | N/A on guest — use SG + private endpoint |
| Network exposure | SG + subnet + host FW | Public vs private endpoint toggle — **you** |
| Identity to data | DB users + VM role | IAM DB auth / roles — **you** |
| Encryption / keys | Configure + own keys | Configure + often choose CMK — **you** |
| Backups | Design and test | Enable, retain, restrict who restores — **you** |
| Logging | Agents + DB audit | Turn on service logs + ship them — **you** |

**Unmanaged** does not mean “ignore the cloud.” The VM still sits in a VPC with cloud IAM. **Managed** does not mean “security done.” A public managed database is still your incident.

## Advanced

### Hardening patterns by surface (provider-agnostic)

**Management plane**

- No daily use of root/owner; break-glass only, logged.  
- Org policies that deny public storage, public admin ports, disabled trails.  
- Separate build credentials from deploy credentials ([6](./6_Supply_Chain_And_Delivery_Integrity.md)).

**Network + firewalls** — detailed layers in [2](./2_Identity_Network_And_Data_Controls.md). Minimum bar: private data planes, no `/0` on admin/DB ports, WAF on public HTTP, egress awareness.

**Compute you patch**

| Practice | Why |
|----------|-----|
| Golden images + rebuild cadence | Drift and CVE debt |
| Patch with evidence | Manual SSH does not scale |
| Bastion / ZTNA / SSM-class admin | Not SSH from the internet |
| Least-privilege instance identity | Stolen guest ≠ whole tenant |
| Disk encryption + private snapshots | Stolen volume / public snapshot |

**Object and block storage**

| Control | Ask |
|---------|-----|
| Public access | Blocked at org policy? |
| IAM vs ACL | Prefer identity policies |
| Encryption / CMK | On by default where required |
| Versioning / immutability | Ransomware and delete recovery |
| Access logging | Sensitive prefixes audited? |

**Data platforms (managed or not)**

| Control | Ask |
|---------|-----|
| Listener | Private only in prod? |
| Auth | Short-lived / IAM-backed over forever passwords in config |
| Admin plane | Who can open the network or export a snapshot? |
| Backups | Encrypted, restore-tested, access-controlled |

**Messaging, LB, DNS, CDN, certificates**

| Resource | Grain |
|----------|-------|
| Queues/topics | Authenticated clients; encrypt payloads; no anonymous |
| LB / gateway | Modern TLS; backends private; admin ports closed |
| DNS | Least privilege on zone changes; registrar lock |
| CDN | Origin locked; signed URLs for sensitive objects |
| Certs | Auto-renew; private keys never in Git |

**SaaS and “not in my VPC”**

Data can leave without touching your security groups. Review tenant admins, external sharing, and OAuth grants on the same cadence as cloud IAM.

### Cross-surface attack paths

1. Open management API key → create public storage → exfil.  
2. Flat VPN → every subnet → unmanaged DB on a VM.  
3. Fat instance role on a web VM → wipe or ransom object stores.  
4. Public managed cache/DB → credential stuffing.  
5. DNS zone IAM open → traffic redirection under your brand.  
6. World-writable queue → poison trusted consumers.  
7. SaaS OAuth persistence → mailbox exfil with empty VPC logs.

CSPM ([3](./3_CSPM_And_Posture.md)) should score **all** surfaces above — not only one runtime.

### Private server, public server, any cloud

| Placement | Still required |
|-----------|----------------|
| Workload on a **public cloud** VM | Cloud IAM + VPC firewalls + guest hardening |
| Workload on a **private cloud** / on-site hypervisor | Management-plane security + same zoning + guest hardening |
| Workload on a **managed service** | Exposure + IAM + encryption + logging you configure |
| **Hybrid** path between them | Consistent identity, firewall policy, and logging on both sides |

“Public server” vs “private server” is a **network placement** choice. It does not replace IAM, patching, or encryption.

## Apply

### Program checklist (works for any provider)

1. Inventory **all** surfaces in scope (table above) — not only app VMs.  
2. Tag each object: managed vs unmanaged; public vs private placement; data class.  
3. Apply [2](./2_Identity_Network_And_Data_Controls.md) controls (identity, firewalls, keys) to every class.  
4. Close internet exposure that is not explicitly required; prefer private endpoints for data.  
5. Enforce org guardrails for public storage and open admin ports.  
6. Secrets out of Git/userdata/images; short-lived workload identity.  
7. Turn on management and high-value data-plane logs ([7](./7_Logging_Detection_And_Cloud_IR.md)).  
8. Feed every class into posture triage ([3](./3_CSPM_And_Posture.md)).  
9. Add runtime-specific gates only where you use them ([5](./5_Containers_Serverless_And_Managed_Runtimes.md)).  
10. Rehearse IR for both “VM compromised” and “managed DB public” stories.

### Self-check

- [ ] Explain cloud security without defaulting to Kubernetes or a three-SKU list  
- [ ] Apply the seven questions (what/who admin/who use/reach/data/change/know/managed) to an arbitrary service  
- [ ] Contrast securing unmanaged Postgres on a VM vs managed Postgres  
- [ ] Name five surfaces beyond compute and object storage  
- [ ] State why SaaS admin planes are in scope even with perfect VPC firewalls  

## Next

- [Containers, serverless, and managed runtimes](./5_Containers_Serverless_And_Managed_Runtimes.md)

## Further reading

- [NIST SP 800-144 — Security and Privacy in Public Cloud Computing](https://csrc.nist.gov/pubs/sp/800/144/final)
- [NIST SP 800-210 — Access Control Guidance for Cloud Systems](https://csrc.nist.gov/pubs/sp/800/210/final)
- [CSA Security Guidance](https://cloudsecurityalliance.org/research/guidance/)
- [CSA Cloud Controls Matrix](https://cloudsecurityalliance.org/research/cloud-controls-matrix)
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
- [ITSP.50.104 — Defence in Depth for Cloud-Based Services](https://www.cyber.gc.ca/en/guidance/guidance-defence-depth-cloud-based-services-itsp50104)
- [AWS Well-Architected — Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)
- [Google Cloud Architecture Framework — Security](https://cloud.google.com/architecture/framework/security)
- [Azure security baselines](https://learn.microsoft.com/en-us/security/benchmark/azure/overview)
- [OWASP Secrets Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
