# Model and data supply chain (AIBOM, signing, MCP)

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

Inventory, provenance, and trust for **models, datasets, tools, and MCP servers**. Catalog: LLM04 / ASI04. Secure development profile: [NIST SP 800-218A](https://csrc.nist.gov/pubs/sp/800/218/a/final). Lifecycle: [CISA/NCSC secure AI guidelines](https://www.cisa.gov/resources-tools/resources/guidelines-secure-ai-system-development). Agent runtime: [4](./4_Agentic_Controls.md).

## Concept

### AI supply chain is wider than “pip install”

| Artifact | Risk if untrusted |
|----------|-------------------|
| Base / fine-tuned **model** weights | Backdoors, unexpected behavior, license/compliance |
| **Datasets** / fine-tune corpora | Poisoning (LLM05) |
| Inference **images** / runtimes | Classic container CVE + GPU stack |
| Orchestrator libraries | Typosquats, malicious plugins |
| **MCP servers** / tool packages | Tool poisoning, hidden instructions in descriptors (ASI04) |
| Prompts / agents-as-code | Unreviewed “prompt malware” |

NIST **SP 800-218A** extends SSDF for generative AI and dual-use foundation models: treat model development with software-security rigor (integrity of training/fine-tune data, secure build/release, etc.).

### SBOM vs AIBOM / ML-BOM

| Artifact | Covers |
|----------|--------|
| **SBOM** | Traditional software components |
| **AIBOM / ML-BOM** | Models, datasets, services, AI pipelines — CycloneDX (ECMA-424) supports ML model/dataset inventory |

Practice: generate and **gate on** SBOMs *and* AI component inventories; attest at build and re-check at deploy.

## Advanced

### Secure AI development lifecycle (CISA/NCSC)

| Stage | Supply-chain habits |
|-------|---------------------|
| **Design** | Threat-model external model hubs, MCP, data brokers ([1](./1_LLM_App_Threat_Model_In_Practice.md)) |
| **Development** | Pin versions; verify signatures; review prompts/tools like code; document model cards |
| **Deployment** | Signed artifacts; least-privilege runtime; secrets out of images |
| **Operation** | Monitor drift; rotate; revoke compromised MCP/tools; patch runtimes |

### Provenance controls (ASI04 mitigations)

| Control | Practice |
|---------|----------|
| Allowlist registries / model hubs | Block arbitrary internet pulls in prod |
| Pin + hash/sign | Models, containers, MCP packages |
| Attestations / signatures on manifests | Prompts, tool defs, agent graphs under review |
| Curated internal mirror | Scan then promote |
| Reproducible builds where feasible | Same input → same artifact |
| Continuous re-validation | Signatures/SBOMs at runtime or deploy time |

### Model Context Protocol (MCP) — trust boundary

[MCP](https://modelcontextprotocol.io/) standardizes how hosts connect to **servers** that expose **resources, prompts, and tools** (JSON-RPC). Power implies risk. Spec security principles:

| Principle | Builder habit |
|-----------|---------------|
| **User consent & control** | Explicit approval for data access and tool actions |
| **Data privacy** | Hosts must not expose user data to servers without consent |
| **Tool safety** | Tools ≈ arbitrary code execution; treat descriptions/annotations as **untrusted** unless from a trusted server |
| **Sampling controls** | Constrain server-initiated model sampling |

```text
Host (your app)
  └─ MCP Client
       └─ MCP Server (local or remote) → tools / resources / prompts
```

**CTI/AppSec habits for MCP:**

| Habit | Why |
|-------|-----|
| Allowlist servers; pin versions | ASI04 |
| Review tool schemas for hidden instructions | Descriptor poisoning |
| Separate identities per server | Confused deputy |
| Network egress controls on servers | Exfil path |
| Include MCP edges in AIBOM | Inventory reality |

### Data integrity for training / fine-tune / RAG ingest

NIST 800-218A emphasizes confirming integrity of training, testing, fine-tuning, and aligning data before use. Pair with [3](./3_RAG_And_Memory_Hardening.md) for retrieval corpora.

| Gate | Check |
|------|-------|
| Source trust | Who produced the data? |
| Integrity | Hash/sign; tamper-evident store |
| Poison review | Sampling / anomaly checks before promote |
| License / privacy | Can we legally and ethically use it? |

### Classic cloud supply chain still applies

Link [Cloud-Security/3](../Cloud-Security/3_Supply_Chain.md): dependency scanning, base images, secrets — AI stacks inherit all of it.

## Apply

1. Inventory AI artifacts in one product (model, datasets, libs, MCP servers, prompts).  
2. Draft an AIBOM/ML-BOM field list (name, version, hash, supplier, license, attestation).  
3. Write an MCP allowlist policy (who may add a server; review checklist).  
4. Map SP 800-218A themes to your CI: which gate blocks a bad model promote?  
5. Threat-register “malicious MCP tool description” → ASI02/ASI04 controls.  
6. Define revoke procedure when a model hub or MCP package is compromised.

### Self-check

- [ ] Name AI-specific supply artifacts beyond normal SBOM  
- [ ] Explain AIBOM/ML-BOM vs SBOM  
- [ ] List MCP host/client/server and tool-safety principle  
- [ ] Tie 800-218A and CISA/NCSC stages to release gates  
- [ ] State pin/sign/allowlist as default prod posture  

## Next

- [Authorized AI evaluation](./6_Authorized_AI_Evaluation.md)

## Further reading

- [NIST SP 800-218A — SSDF Community Profile for GenAI](https://csrc.nist.gov/pubs/sp/800/218/a/final)
- [CISA / NCSC — Guidelines for secure AI system development](https://www.cisa.gov/resources-tools/resources/guidelines-secure-ai-system-development)
- [Model Context Protocol — Specification](https://modelcontextprotocol.io/specification/2025-11-25)
- [CycloneDX — ML-BOM](https://cyclonedx.org/capabilities/mlbom/)
- [OWASP LLM04 / ASI04 (Top 10 2026 hubs)](https://genai.owasp.org/llm-top-10/)
- [Cloud-Security/3 — Supply chain](../Cloud-Security/3_Supply_Chain.md)
