# Assets, data states, and assurance

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

You cannot protect what you have not identified. This note covers asset and data literacy that sits under every later control discussion.

## Concept

## Assets

| Asset type | Examples |
|------------|----------|
| Information | Databases, files, secrets, backups, logs |
| Software | Applications, containers, firmware, models |
| Hardware | Laptops, servers, phones, tokens, network gear |
| Services | APIs, identity providers, SaaS, pipelines |
| People and facilities | Operators, badges, datacenter space (physical) |

Inventory needs owners, data class ([1](./1_CIA_And_Risk.md)), and criticality (from BIA thinking in [5](./5_Risk_Management_Process_And_RMF.md)). Shadow IT is an asset discovery problem as much as a policy problem.

## Data states

| State | Meaning | Typical controls |
|-------|---------|------------------|
| **At rest** | Stored on disk, object store, backup, laptop | Access control; encryption at rest; key management |
| **In transit** | Moving across a network | TLS/IPsec; authenticated channels; certificate validation |
| **In use** | Being processed in memory / CPU | Least privilege; isolation; careful logging; confidential-computing options where required |

```text
create/collect → at rest → in transit → in use → at rest → retain/dispose
```

A control that only covers “in transit” leaves backups and snapshots exposed. Design for all three states the data actually occupies.

## Advanced

## Data handling basics

| Concern | Practice |
|---------|----------|
| Classification | Label and handle per class |
| Minimization | Collect and keep only what the mission needs |
| Retention | Keep for the required period; delete or archive on schedule |
| Disposal | Crypto-erase or destroy media; revoke tokens |
| Sharing / DLP | Control egress for sensitive classes |
| Anonymization / pseudonymization | Reduce identifiability when full identity is not required for the job |

| Data flavor (literacy) | Why it matters |
|------------------------|----------------|
| PII / personal data | Privacy duties and breach impact ([17](./17_Privacy_Literacy.md)) |
| Secrets / credentials | Compromise impersonates people or workloads ([8](./8_Cryptographic_Solutions_Literacy.md)) |
| Regulated / sector data | Extra retention, residency, or access rules |

Privacy overlays purpose limitation and individual rights on top of these technical handling rules ([4](./4_Security_Policy_Governance_And_Roles.md)).

## Baselines and hardening (literacy)

A **secure baseline** is a documented, approved configuration for a platform (OS image, container, cloud account guardrails). Drift away from baseline is a detective signal. CIS-class benchmarks and cloud posture tools implement this idea. Endpoint/system literacy: [15](./15_Endpoint_And_System_Hardening_Literacy.md). Cloud posture: [Cloud-Security](../Cloud-Security/README.md).

## Assurance activities (map)

| Activity | Question |
|----------|----------|
| Design review / threat model | Are the right controls planned? ([12](./12_STRIDE_And_Threat_Modeling.md)) |
| Implementation review | Were they built as designed? |
| Control assessment | Do they operate and produce results? |
| Continuous monitoring | Are they still effective after change? |
| Authorization | Is residual risk acceptable for operation? ([5](./5_Risk_Management_Process_And_RMF.md)) |

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] List five asset types with one example each  
- [ ] Give one control for data at rest, in transit, and in use  
- [ ] Explain why backup encryption matters even if TLS is perfect  
- [ ] Define secure baseline in one sentence  

## Next

- [AAA and access fundamentals](./7_AAA_And_Access_Fundamentals.md)

## Further reading

- [NIST SP 800-12 Rev. 1](https://csrc.nist.gov/pubs/sp/800/12/r1/final)
- [NIST SP 800-60 Vol. 1 Rev. 1 — Guide for Mapping Types of Information and Information Systems to Security Categories](https://csrc.nist.gov/pubs/sp/800/60/v1/r1/final)
- [NIST SP 800-88 Rev. 1 — Guidelines for Media Sanitization](https://csrc.nist.gov/pubs/sp/800/88/r1/final)
