# Cloud security

[← README](../README.md)

Cloud computing models, shared responsibility, and how to secure cloud estates across providers and deployment types: identity, network and firewall controls, data and keys, continuous posture, resources and workloads (managed and self-operated), delivery integrity, cloud-shaped detection and response, and AI workloads hosted in the cloud.

Product click-paths live in [DevOps-Handbook](https://github.com/thisiskushal31/DevOps-Handbook). Wire and TLS depth live in [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive). Orchestrator depth lives in [Containerization-Deep-Dive](https://github.com/thisiskushal31/Containerization-Deep-Dive). Federation and zero trust live in [Identity](../Identity/README.md). SIEM and classical IR live in [Defensive-Ops](../Defensive-Ops/README.md). Application defects live in [AppSec](../AppSec/README.md). LLM/agent application practice lives in [AI-Security](../AI-Security/README.md).

Each note follows **concept → advanced → apply**. Official Further reading only. Lab/adversarial work stays **authorized and isolated** — [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

```text
1 Cloud models & shared responsibility → 2 Identity, network & data
  → 3 CSPM (posture) → 4 Resources & workloads
    → 5 Containers, serverless & managed runtimes
      → 6 Supply chain & delivery integrity
        → 7 Logging, detection & cloud IR
          → 8 AI workloads & cloud AI security
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [Cloud models and shared responsibility](./1_Cloud_Models_And_Shared_Responsibility.md) |
| 2 | [Identity, network, and data controls](./2_Identity_Network_And_Data_Controls.md) |
| 3 | [CSPM and continuous posture](./3_CSPM_And_Posture.md) |
| 4 | [Securing cloud resources and workloads](./4_Securing_Cloud_Resources_And_Workloads.md) |
| 5 | [Containers, serverless, and managed runtimes](./5_Containers_Serverless_And_Managed_Runtimes.md) |
| 6 | [Supply chain and delivery integrity](./6_Supply_Chain_And_Delivery_Integrity.md) |
| 7 | [Logging, detection, and cloud IR](./7_Logging_Detection_And_Cloud_IR.md) |
| 8 | [AI workloads and cloud AI security](./8_AI_Workloads_And_Cloud_AI_Security.md) |

Learning path **1 → 8** complete for this folder.

## Learning path

`1 → 2 → 3 → 4 → 5 → 6 → 7 → 8`

## Topic map

| Area | Home |
|------|------|
| NIST cloud vocabulary; public/private/community/hybrid; IaaS/PaaS/SaaS (Infrastructure/Platform/Software as a Service); managed vs self-operated; shared responsibility | 1 |
| IAM (Identity and Access Management); firewall and network zoning; encryption and key management | 2 |
| Misconfiguration; CSPM / CIEM / CWPP / CNAPP literacy (see note 3 for full forms); toxic combinations; CIS / CCM | 3 |
| Management plane, network fabric, compute, storage, data, messaging, edge, certificates, SaaS, pipelines | 4 |
| Containers and Kubernetes, FaaS (Function as a Service), managed application platforms | 5 |
| SBOM (Software Bill of Materials); SLSA (Supply-chain Levels for Software Artifacts); sign and attest; CI trust; A03/A08 delivery controls | 6 |
| Telemetry; detections; cloud IR (incident response); AI-assisted monitoring guardrails | 7 |
| AI/ML cloud deploy; GPU pools; AI-SPM (AI Security Posture Management); managed vs self-hosted serve; AI-assisted ops vs AI systems | 8 |

## Related homes (do not duplicate)

| Topic | Home |
|-------|------|
| App Top 10 / ASVS (Application Security Verification Standard) | [AppSec](../AppSec/README.md) |
| LLM/agent application security | [AI-Security](../AI-Security/README.md) |
| Federation / zero trust (ZT) | [Identity](../Identity/README.md) |
| SIEM (Security Information and Event Management) / classical IR | [Defensive-Ops](../Defensive-Ops/README.md) |
| Vuln triage | [Vulnerability-Management](../Vulnerability-Management/README.md) |
| Crypto pitfalls | [Cryptography](../Cryptography/README.md) |
| GRC / CCM (Cloud Controls Matrix) nesting | [GRC](../GRC/README.md) |
| Wire / TLS (Transport Layer Security) depth | [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive) |
| Cloud product literacy | [DevOps-Handbook Cloud](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Cloud) |
| Orchestrator runtime depth | [Containerization-Deep-Dive](https://github.com/thisiskushal31/Containerization-Deep-Dive) |
| AI serving performance (non-security) | [Inference-And-Serving](https://github.com/thisiskushal31/Data-Science-AI-Deep-Dive/tree/main/Inference-And-Serving) |
