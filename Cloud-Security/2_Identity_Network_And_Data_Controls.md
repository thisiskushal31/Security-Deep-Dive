# Identity, network, firewalls, and data controls

[← Cloud-Security](./README.md)

> **Progression:** concept → advanced → apply.

Securing a cloud means controlling **who can call the APIs**, **which traffic is allowed** (firewalls and network settings), and **how data and keys are protected**. These controls apply on AWS, Azure, GCP, a private OpenStack/VMware-class cloud, or a hybrid of all of them — the *names* of security groups and NACLs change; the *jobs* do not.

Product click-paths: Handbook. Federation / ZT theory: [Identity](../Identity/README.md). Packet/TLS depth: [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive). Crypto pitfalls: [Cryptography](../Cryptography/README.md). Everything you attach these controls to: [4](./4_Securing_Cloud_Resources_And_Workloads.md).

## Concept

### Identity — the real perimeter of the management plane

Cloud **IAM (Identity and Access Management)** is usually **policy on every API call**. Console, CLI, Terraform, and attackers all hit the same plane.

| Practice | Why |
|----------|-----|
| **Least privilege** | Only the actions and resources required |
| **Short-lived credentials** | Roles/federation over forever access keys |
| **MFA (multi-factor authentication) for humans** | Console, break-glass, privileged roles |
| **Human ≠ workload identity** | Pipelines and VMs must not share laptop SSO (single sign-on) |
| **JIT (just-in-time) elevation** | Standing admin is standing risk |
| **Org guardrails** (SCPs / Azure Policy / org policies / private-cloud equivalents) | Project owners cannot silently open the world |
| **Access review** | Unused roles and keys are unpaid surface |

```text
Human ──SSO/MFA──▶ IdP ──federation──▶ Cloud IAM / directory
Workload ──instance / OIDC (OpenID Connect) identity──▶ role / service account
                                         │
                                         ▼
                            API authorize (policy + conditions)
```

### Firewalls and network settings — layered, not one checkbox

“Set the firewall” in cloud usually means **several layers** working together:

| Layer | What it is | Typical job |
|-------|------------|-------------|
| **Edge / internet gateway path** | How a VPC/VNet reaches the internet or users | Control what is even routable publicly |
| **NACL (network access control list) / subnet filter** | Stateless allow/deny at subnet edge | Coarse zone boundary |
| **Security group / NSG (network security group) / stateful filter** | Attached to NICs / instances / ENIs | Allow-list east-west and north-south for workloads |
| **Host firewall** (iptables/nftables/Windows FW) | Inside the guest OS you manage | Defense-in-depth when you run the OS |
| **Network virtual appliance / NGFW (next-generation firewall)** | Third-party or marketplace firewall VM/appliance | Deep inspection, IPS (intrusion prevention), centralized policy |
| **WAF (web application firewall) / API gateway protections** | HTTP(S) application filter | SQLi/XSS/bot abuse in front of apps |
| **DDoS (distributed denial of service) / volumetric protections** | Provider edge or dedicated service | Absorb or scrub floods |
| **Private endpoints / service endpoints** | Private path to managed PaaS (Platform as a Service) | Avoid public service URLs for data planes |
| **Egress controls / proxies** | Outbound allow lists | Limit exfil and malware callbacks |

```text
Internet / partner
        │
   [edge / DDoS / WAF]          ← application & volumetric
        │
   [NACL / subnet ZIP]          ← coarse zone
        │
   [SG / NSG / NGFW]            ← stateful / deep inspection
        │
   workload NIC ── host FW      ← guest you manage (unmanaged OS)
        │
   private endpoint ── managed DB / storage   ← no public listener
```

**Zoning idea (any provider):** separate management, presentation, application, and data paths. Do not put databases on the same trust as internet-facing listeners. Hybrid interconnects need the **same** zone discipline on both ends — a flat VPN into every subnet recreates a flat enterprise LAN in the cloud.

### Data and keys

| Concern | Practice |
|---------|----------|
| Classification | Know sensitivity before you pick a store |
| Encryption in transit | TLS (Transport Layer Security) for clients and service-to-service |
| Encryption at rest | Disks, objects, databases, backups |
| Key management | KMS (Key Management Service) / HSM (Hardware Security Module); separate **users** of keys from **admins** of keys |
| Customer-managed keys (CMK) | When policy requires you hold the trust root |
| Access path | Prefer app-tier access over broad human read of prod data |

## Advanced

### Firewall mistakes that show up everywhere

| Mistake | Effect |
|---------|--------|
| `0.0.0.0/0` on SSH/RDP/DB/admin ports | Internet-wide brute force and exploit scanning |
| Security group “temporary open” never closed | Permanent exposure |
| Only SG, no egress discipline | Compromised workload phones home freely |
| WAF off because “we have a SG” | SG does not stop application-layer abuse |
| NGFW in hairpin path nobody understands | Shadow routes bypass the appliance |
| Trust “corp IP” on VPN as full admin | Stolen laptop = cloud admin |
| Public managed-DB endpoint “for convenience” | Entire data plane on the internet |

### Managed vs unmanaged network duty

| You operate the OS / appliance (unmanaged guest) | Provider operates the service (managed) |
|--------------------------------------------------|----------------------------------------|
| Host firewall, patch, agent, hardening baseline | You still set **exposure**, IAM, private link, TLS, logging |
| You may deploy NGFW VMs yourself | Provider may offer native WAF/DDoS — **you must turn them on and scope them** |
| Routing mistakes are yours | Mis-attached public endpoint is still yours |

Shared responsibility ([1](./1_Cloud_Models_And_Shared_Responsibility.md)) never means “network security is free.”

### Identity failure modes

| Failure | Effect |
|---------|--------|
| Long-lived keys in CI or laptops | Persistent foothold |
| `*:*` admin for convenience | Any leak becomes org-wide |
| Break-glass without logging | No IR accountability |
| Trusting only network location | VPN user inherits flat access |
| SaaS OAuth grants forgotten | Third parties keep mailbox/drive power |

### Provider-agnostic pattern checklist

| Pattern | Public cloud | Private cloud | Hybrid |
|---------|--------------|---------------|--------|
| East-west limit | SG/NSG + zones | SDN + firewalls | Both ends of interconnect |
| Admin access | Bastion / SSM-class / ZTNA (Zero Trust Network Access) — never SSH `/0` | Jump hosts + MFA | Same policy language |
| PaaS data plane | Private endpoint | Internal-only URL | Consistent DNS/routing |
| Metadata / IMDS (Instance Metadata Service) | Restrict hop/path | Hypervisor equivalents | Do not bridge trusts |

## Apply

1. Draw zones: internet edge → app → data → management.  
2. Default-deny; allow-list SG/NSG rules; ban `/0` on admin and data ports in policy.  
3. Put WAF (or equivalent) in front of internet HTTP apps.  
4. Constrain egress from sensitive workloads.  
5. Humans: SSO+MFA; workloads: short-lived identity.  
6. Encrypt data paths; name key admins separately from app roles.  
7. Hybrid: document which firewalls enforce which zone on each side.  
8. Log allow/deny and IAM auth for IR ([7](./7_Logging_Detection_And_Cloud_IR.md)).

### Self-check

- [ ] Name at least five firewall/network layers and what each blocks  
- [ ] Explain why a security group is not a WAF  
- [ ] Contrast managed PaaS network duty vs unmanaged VM host firewall  
- [ ] Describe a safe admin-access pattern without public SSH  
- [ ] Separate key *use* from key *administration*  

## Next

- [CSPM and continuous posture](./3_CSPM_And_Posture.md)

## Further reading

- [NIST SP 800-210 — Access Control Guidance for Cloud Systems](https://csrc.nist.gov/pubs/sp/800/210/final)
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [CSA Security Guidance — IAM / Infrastructure domains](https://cloudsecurityalliance.org/research/guidance/)
- [Canadian Centre for Cyber Security — ITSP.50.104 Defence in Depth for Cloud](https://www.cyber.gc.ca/en/guidance/guidance-defence-depth-cloud-based-services-itsp50104)
- [Canadian Centre for Cyber Security — ITSP.80.023 Cloud Network Security Zones](https://www.cyber.gc.ca/en/guidance/cloud-network-security-zones-itsp80023)
- [AWS Well-Architected — Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html)
- [Google Cloud Architecture Framework — Security](https://cloud.google.com/architecture/framework/security)
- [Azure security baselines](https://learn.microsoft.com/en-us/security/benchmark/azure/overview)
- [Identity deep dive](../Identity/README.md)
- [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive)
