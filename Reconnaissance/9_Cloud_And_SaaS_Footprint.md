# Cloud and SaaS footprint

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

Modern attack surface is **accounts and URLs**, not only CIDRs. Cloud misconfigs and forgotten SaaS tenants dominate real exposure. Product posture: [Cloud-Security](../Cloud-Security/README.md). Exposure indexes: [5](./5_Internet_Exposure_And_ASM.md) · [Shodan](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Shodan).

## Concept

### What “cloud recon” means

| Object | Public clues | Typical failure |
|--------|--------------|-----------------|
| Object storage | Guessable bucket names; listable | Public read/write |
| Compute / LB | IP + cert names | Management ports open |
| IaC leftovers | `.tfstate`, pipeline logs | Secrets in public artifacts |
| SaaS tenants | `company.saas.com`, SSO realm | Orphaned tenants still live |
| Serverless / APIs | Gateway URLs in JS | No auth on “internal” functions |
| Container registries | Public images with secrets | Credential leak |

```text
DNS + CT + org search + cloud inventory APIs (authorized)
        │
        ▼
 tenant / bucket / API map
        │
        ▼
 close public ACLs · MFA · CSPM tickets
```

### Passive clues before touching cloud APIs

- Certificate names mentioning `s3`, `azure`, `cloudfront`, region strings.  
- MX / TXT proving Microsoft 365 / Google Workspace.  
- Public Git: account IDs, bucket strings, `AKIA…` patterns (rotate if real).  
- Exposure-search product filters for cloud storage / panels.  
- Job posts naming exact cloud + SaaS stacks.

## Advanced

### Authorized cloud inventory beats guessing

With **your** cloud org credentials (or contracted assessment access):

| Check | Why |
|-------|-----|
| Public storage ACLs | Classic breach class |
| Security groups / NSGs with `0.0.0.0/0` | Edge reality |
| Unused public IPs | Shadow services |
| IAM users with old keys | Spray / leak impact |
| Cross-account trusts | Lateral path |
| Public AMIs / snapshots | Data residue |

CSPM (Cloud Security Posture Management) / CNAPP literacy: [Cloud-Security/3](../Cloud-Security/3_CSPM_And_Posture.md).

### SaaS footprint

| Habit | Risk if skipped |
|-------|-----------------|
| SSO inventory of corporate apps | Shadow SaaS with weak passwords |
| Offboarding removes SaaS access | Ex-employee persistence |
| Admin audit logs on IdP | Blind to OAuth app grants |
| DLP on drive/share links | Public link sprawl |
| Tenant lifecycle (create → use → retire) | Forgotten staging tenants |

### Multi-cloud and shared responsibility

Recon must track **which plane** owns a control: CSP (Cloud Service Provider) vs customer. Public storage is almost always a customer misconfig; physical DC access is not. Don’t confuse “cloud is secure” marketing with your ACL reality.

### What not to do

Do not brute foreign bucket namespaces “for practice.” Do not probe random `*.amazonaws.com` objects. Use **your** accounts, vendor ASM features, and lab orgs. Same rule as [2](./2_Scope_Authorization_And_Evidence.md).

### Tie-back to network and web recon

Public cloud IPs still need [6](./6_Network_And_Host_Discovery.md); HTTP apps on them need [7](./7_Web_And_App_Surface.md). Cloud recon adds the **account / tenant / identity** dimension those notes don’t own.

## Apply

1. In a cloud account you own: list public storage + `0.0.0.0/0` admin ports.  
2. Passively list SaaS products your org’s DNS/mail imply.  
3. Open tickets for any public storage or orphaned tenant.  
4. Confirm one CSPM or native config finding matches what exposure search already showed.

## Further reading

- [CIS Benchmarks — cloud providers](https://www.cisecurity.org/cis-benchmarks)
- [CISA — Cloud security technical reference architecture](https://www.cisa.gov/resources-tools/resources/cloud-security-technical-reference-architecture)
- [Cloud-Security](../Cloud-Security/README.md)
- [MITRE ATT&CK — Cloud](https://attack.mitre.org/matrices/enterprise/cloud/)
- [NIST SP 800-210 — General Access Control Guidance for Cloud Systems](https://csrc.nist.gov/publications/detail/sp/800-210/final)
