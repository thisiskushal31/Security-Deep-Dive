# AI workloads and cloud AI security

[← Cloud-Security](./README.md)

> **Progression:** concept → advanced → apply.

AI systems usually train and serve in cloud or hybrid environments because they need elastic compute, storage, and GPUs. This note is the **cloud deployment and operations** slice: how AI services sit on the same surfaces as any other workload, what extra controls they need, and how AI-assisted security tooling fits a cloud program. Prompt injection, RAG (retrieval-augmented generation), agents, and model-theft literacy live in [AI-Security](../AI-Security/README.md). General resource hardening: [4](./4_Securing_Cloud_Resources_And_Workloads.md). Delivery integrity: [6](./6_Supply_Chain_And_Delivery_Integrity.md).

## Concept

### Two different “AI in cloud” problems

| Problem | Meaning | Primary home |
|---------|---------|--------------|
| **Secure AI deployments** | Training jobs, model registries, inference endpoints, GPU pools, vector stores, agent runtimes hosted on cloud | This note + [AI-Security](../AI-Security/README.md) |
| **AI-assisted cloud security** | Using analytics or generative tools to triage posture, detections, and investigations | This note ([7](./7_Logging_Detection_And_Cloud_IR.md) for monitoring) + [Defensive-Ops](../Defensive-Ops/README.md) |

Do not conflate them. A hardened inference VPC does not fix unsafe SOC (Security Operations Center) prompts that paste CloudTrail into a public chatbot.

### Shared responsibility still applies

| You typically own | Provider typically owns (managed AI PaaS — Platform as a Service) |
|-------------------|-------------------------------------------|
| IAM (Identity and Access Management) to projects, buckets, endpoints, keys | Physical / hypervisor / regional fabric |
| Network exposure of training and inference | Managed service runtime under the API (varies by SKU) |
| Data used for train/fine-tune/RAG; classification; DLP (data loss prevention) | Platform compliance of the *service* (not your prompts/data misuse) |
| Model artifact integrity and access | Underlying host patch for fully managed endpoints |
| Logging, monitoring, IR for *your* tenant | Abuse desk / infra forensics limits |

Self-hosted inference on VMs or Kubernetes shifts OS/runtime patching back to you — same managed vs self-operated split as [1](./1_Cloud_Models_And_Shared_Responsibility.md).

### Inventory before clever controls

| Asset | Why it matters |
|-------|----------------|
| Model endpoints (public or private) | Unauthenticated inference is immediate abuse surface |
| Training/fine-tune datasets and feature stores | Poisoning, leakage, residency |
| Model weights / checkpoints / registries | Theft, tampering, supply-chain substitution |
| GPU / accelerator pools | Cost abuse, weak visibility vs CPU EDR (endpoint detection and response) norms |
| Vector DBs / caches used for RAG | Data exfil and poison paths |
| Agent tool credentials and MCP (Model Context Protocol)-class connectors | Privilege amplification ([AI-Security/4–5](../AI-Security/README.md)) |
| CI jobs that build or promote models | Same promote controls as other artifacts ([6](./6_Supply_Chain_And_Delivery_Integrity.md)) |

```text
Data / features ──▶ train / fine-tune ──▶ model registry
                                              │
                         serve (managed endpoint | GPU VM | cluster)
                                              │
                         clients / agents / apps ── tools & data stores
```

## Advanced

### Secure the deployment environment

Align with joint guidance themes for deploying AI systems:

| Theme | Cloud practice |
|-------|----------------|
| **Governance** | Name owners for each model service; change control for endpoint exposure |
| **Architecture** | Separate train, stage, and serve accounts/projects where risk warrants |
| **Hardened config** | Private endpoints; no anonymous invoke; org policies against public model APIs |
| **Network** | Egress allow lists from training/inference; block metadata abuse; segment GPU pools |
| **Identity** | Workload identities to pull models and data — not long-lived keys in notebooks |

### Continuously protect the AI system

| Control | Practice |
|---------|----------|
| **Validate before and during use** | Version pins; evaluation gates before promote ([AI-Security/6](../AI-Security/6_Authorized_AI_Evaluation.md)) |
| **Secure exposed APIs** | Authn/z, rate limits, WAF (web application firewall) / API gateway in front of HTTP inference |
| **Monitor behavior** | Unusual invoke volume, prompt/tool anomaly signals, egress spikes |
| **Protect model weights** | Encrypt at rest; least privilege on registries; signed artifacts where feasible |
| **Input / output filtering** | Application-layer controls for injection and sensitive egress ([AI-Security/2](../AI-Security/2_Prompt_Injection_Defenses.md)) |

### Operate and maintain

| Control | Practice |
|---------|----------|
| Strict access reviews on model admins and data scientists with prod reach | |
| Patch base images, serving stacks, and managed runtime versions | |
| Logging of invoke, admin, and data-access events into the same IR path as [7](./7_Logging_Detection_And_Cloud_IR.md) | |
| Backups / rebuild for registries and configs; secure delete for sensitive training sets | |
| Tabletop AI-specific incidents (stolen weights, public endpoint, poisoned RAG store) | |

### GPU and accelerator notes

Accelerators are compute with weaker historical security tooling visibility. Prefer:

- Isolated pools for sensitive models  
- Controlled image/boot chains for GPU nodes  
- Network and IAM as primary containment (do not assume CPU EDR sees GPU memory)  
- Cost and quota alerts (crypto-mining-class abuse of exposed GPUs)

Confidential computing options exist for some regulated deployments — treat as a targeted control, not a substitute for API auth and network design ([2](./2_Identity_Network_And_Data_Controls.md)).

### Managed AI PaaS vs self-hosted serving

| Mode | Emphasize |
|------|-----------|
| **Managed model/API service** | Tenant IAM, private networking, data governance, logging export, contractual data-use terms |
| **Self-hosted (vLLM-class, custom stacks)** | Image supply chain, host/cluster hardening ([5](./5_Containers_Serverless_And_Managed_Runtimes.md)), registry signing, egress, authn in front of the server |
| **Notebooks / ML platforms** | Idle public notebooks, embedded secrets, over-broad project roles |

Serving performance literacy (KV cache, scaling) is not this folder’s job — see Data-Science Inference-And-Serving for that slice.

### AI posture — AI-SPM (AI Security Posture Management)

Extend CSPM (Cloud Security Posture Management) questions to AI objects ([3](./3_CSPM_And_Posture.md)):

| Ask | Example finding |
|-----|-----------------|
| Is the endpoint public? | Internet-open inference without auth |
| Who can invoke / admin / download weights? | Broad roles on registry and endpoint |
| Which data stores feed train/RAG? | Unencrypted or over-shared buckets |
| Are keys and tool secrets short-lived? | Static tokens in notebook env |
| Is shadow AI using unapproved SaaS (Software as a Service)? | Governance in [AI-Security/7](../AI-Security/7_Shadow_AI_And_Private_GenAI.md) |

CSA (Cloud Security Alliance) **AICM (AI Controls Matrix)** maps AI-specific control language next to CCM (Cloud Controls Matrix)-style programs — use for assurance mapping, not as a paste-all checklist.

### AI-assisted cloud security operations

| Use | Guardrail |
|-----|-----------|
| Summarize noisy posture or SIEM queues | Analyst remains accountable for priority and action |
| Draft investigation timelines from logs | Keep raw evidence; verify citations |
| Suggest remediations | Dual-control before applying org-wide policy or key disable |
| Enrich detections | Do not send unrestricted prod telemetry to untrusted model hosts |

## Apply

1. Inventory AI endpoints, registries, GPU pools, vector stores, and notebook platforms.  
2. Close unauthenticated public inference; prefer private endpoints + strong identity.  
3. Apply [2](./2_Identity_Network_And_Data_Controls.md) and [4](./4_Securing_Cloud_Resources_And_Workloads.md) to train/serve accounts like any high-value system.  
4. Sign/pin model artifacts and promote through controlled pipelines ([6](./6_Supply_Chain_And_Delivery_Integrity.md)).  
5. Turn on invoke and admin logging; wire to IR ([7](./7_Logging_Detection_And_Cloud_IR.md)).  
6. Add AI objects to posture triage (AI-SPM questions above).  
7. Separate “AI for SOC assistance” from “AI production systems” — different data handling.  
8. Hand application-layer LLM/agent threats to [AI-Security](../AI-Security/README.md); do not re-home them here.

### Self-check

- [ ] Separate securing AI deployments from AI-assisted SOC tooling  
- [ ] Draw train → registry → serve and name one control per stage  
- [ ] Explain managed AI PaaS vs self-hosted inference duties  
- [ ] List three AI-SPM questions beyond generic CSPM  
- [ ] State why GPU pools need network/IAM emphasis  
- [ ] Name where prompt/RAG/agent depth lives outside this folder  

## Related

- [AI-Security](../AI-Security/README.md) — LLM/agent application practice  
- [Threats/8](../Threats/8_AI_Era_Threats.md) — AI-era threat literacy  
- [Vulnerability-Management](../Vulnerability-Management/README.md) — prioritization of findings  

## Further reading

- [NSA/CISA et al. — Deploying AI Systems Securely](https://www.cisa.gov/news-events/alerts/2024/04/15/joint-guidance-deploying-ai-systems-securely)
- [Deploying AI Systems Securely (PDF)](https://media.defense.gov/2024/Apr/15/2003439257/-1/-1/0/CSI-DEPLOYING-AI-SYSTEMS-SECURELY.PDF)
- [CISA — AI Data Security](https://www.cisa.gov/resources-tools/resources/ai-data-security-best-practices-securing-data-used-train-operate-ai-systems)
- [NIST AI 100-1 — AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST SP 800-218A — SSDF (Secure Software Development Framework) Community Profile for Generative AI](https://csrc.nist.gov/pubs/sp/800/218/a/final)
- [CSA Security Guidance](https://cloudsecurityalliance.org/research/guidance/)
- [CSA AI Controls Matrix](https://cloudsecurityalliance.org/artifacts/ai-controls-matrix-v1-1)
- [Google — Best practices for securely deploying AI on Google Cloud](https://services.google.com/fh/files/misc/best-practices-for-securely-deploying-ai-on-google-cloud.pdf)
- [Google Cloud — AI workload security on GKE](https://cloud.google.com/kubernetes-engine/security/ai-security-best-practices)
- [NCSC et al. — Guidelines for secure AI system development](https://www.ncsc.gov.uk/collection/guidelines-secure-ai-system-development)
