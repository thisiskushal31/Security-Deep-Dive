# Model and data supply chain (AIBOM, signing, MCP)

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

Inventory, provenance, and trust for **models, datasets, tools, and MCP servers**. Catalog: LLM04 / ASI04. Agent runtime: [4](./4_Agentic_Controls.md). How supply-chain plants show up as “the agent did it”: [8](./8_Attack_Mechanisms_For_Defenders.md).

## Concept

### AI supply chain is wider than “pip install”

Your agent’s behavior is the composition of every artifact that shaped it. Compromise any link and the app looks “normal” while executing someone else’s intent.

| Artifact | Risk if untrusted | Typical plant path |
|----------|-------------------|--------------------|
| Base / fine-tuned **model** weights | Backdoors, unexpected behavior, license/compliance | Hub pull, merge, LoRA from unknown source |
| **Datasets** / fine-tune corpora | Poisoning (LLM05) | Contaminated open data; insider insert |
| Inference **images** / runtimes | Container CVE + GPU stack | Untagged `latest`, public base images |
| Orchestrator libraries | Typosquats, malicious plugins | Package registry |
| **MCP servers** / tool packages | Tool poisoning; hidden instructions in descriptors (ASI04) | “Helpful” community server |
| Prompts / agents-as-code | Unreviewed “prompt malware” | Shared prompt repo without review |

Treat generative-AI development with software-security rigor: integrity of training and fine-tune data, secure build and release, signed promote paths.

### SBOM vs AIBOM / ML-BOM

| Artifact | Covers |
|----------|--------|
| **SBOM** | Traditional software components |
| **AIBOM / ML-BOM** | Models, datasets, services, AI pipelines — CycloneDX supports ML model/dataset inventory |

Practice: generate and **gate on** SBOMs *and* AI component inventories; attest at build and re-check at deploy. An AIBOM that omits MCP edges is fiction.

### How a supply-chain plant becomes an app incident

1. Developer or CI pulls a model, MCP server, or prompt pack outside an allowlist (or from a compromised allowlisted version).
2. Tool descriptors or system prompts include attacker instructions or excessive scopes.
3. At runtime the host trusts those descriptors; the model “helpfully” calls tools as described.
4. Privileged side effects execute under the agent’s identity — the attacker never authenticated to your DB.

```text
Untrusted artifact (model / MCP / prompt / dataset)
  → Build or laptop installs it
    → Runtime trusts descriptors & weights
      → Agent acts with your privileges
```

Pin, sign, allowlist, and review — or you are threat-modeling the wrong boundary.

## Advanced

### Secure AI development lifecycle

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
| Revocation runbook | Known-bad model/MCP → kill sessions, unpin, rotate secrets |

### Model Context Protocol (MCP) — trust boundary

The Model Context Protocol standardizes how an AI **host** application connects to **servers** that expose **resources, prompts, and tools** over JSON-RPC. The host creates **one client per server**; each client keeps a dedicated session. Local servers often use STDIO; remote servers often use HTTP-style transports. Either way, a server is code-shaped power sitting next to your agent.

```text
                    MCP Host (your AI app / IDE / agent runtime)
                    ┌─────────────────────────────────────────┐
                    │  Client₁    Client₂    Client₃          │
                    └───┬───────────┬───────────┬─────────────┘
                        │           │           │
                   ┌────▼───┐  ┌────▼───┐  ┌────▼────┐
                   │Server A│  │Server B│  │Server C │
                   │(local) │  │(local) │  │(remote) │
                   └────┬───┘  └────┬───┘  └────┬────┘
                        │           │           │
                     files/DB    tools API    SaaS API
```

Security principles that matter in production:

| Principle | Builder habit |
|-----------|---------------|
| **User consent & control** | Explicit approval for data access and tool actions |
| **Data privacy** | Do not expose user data to servers without consent |
| **Tool safety** | Tools ≈ arbitrary code execution; treat descriptions/annotations as **untrusted** unless the server is strongly trusted |
| **Sampling controls** | Constrain server-initiated model sampling loops |

**How compromise shows up:** a low-trust plant (issue text, ticket, malicious package) is read by a host that holds elevated MCP tools — the agent, not the attacker, performs the privileged action ([8](./8_Attack_Mechanisms_For_Defenders.md)).

| Habit | Why |
|-------|-----|
| Allowlist servers; pin versions + hashes | Stop arbitrary server pull-ins |
| Review **full** tool definitions (description, `inputSchema`, annotations) | **Tool poisoning** — instructions hidden where UI shows a short label |
| Hash complete defs; alert on change after approval | **Rug pull** — trusted tool becomes malicious without a version bump people notice |
| Isolate privileged tools from external MCP context | **Tool shadowing** — malicious server steers use of other servers’ tools |
| Per-client consent on MCP proxies; no static-ID consent cookies alone | **Confused deputy** — stolen auth codes / tokens without real consent |
| Separate identities per server; least privilege | Confused deputy at the identity layer |
| Network egress controls on servers | Exfil path |
| Include MCP edges in AIBOM | Inventory reality |
| Never grant production `service_role` to chat agents by default | Ticket / issue text must not equal DB root (published MCP failure pattern) |
| Structured tool responses + schema reject | Free-text tool returns are another injection channel |

### Data integrity for training / fine-tune / RAG ingest

Confirm integrity of training, testing, fine-tuning, and alignment data before use. Pair with [3](./3_RAG_And_Memory_Hardening.md) for retrieval corpora — the same poison can hit training *or* RAG.

| Gate | Check |
|------|-------|
| Source trust | Who produced the data? |
| Integrity | Hash/sign; tamper-evident store |
| Poison review | Sampling / anomaly checks before promote |
| License / privacy | Can we legally and ethically use it? |
| Promote dual-control | High-impact fine-tunes need a second reviewer |

### Classic cloud supply chain still applies

Dependency scanning, base images, secrets — AI stacks inherit all of it. Link [Cloud-Security/3](../Cloud-Security/3_Supply_Chain.md); do not invent a parallel universe where “it’s just weights.”

## Apply

1. Inventory AI artifacts in one product (model, datasets, libs, MCP servers, prompts).  
2. Draft an AIBOM/ML-BOM field list (name, version, hash, supplier, license, attestation).  
3. Write an MCP allowlist policy (who may add a server; review checklist for descriptors).  
4. Map secure-development stages to your CI: which gate blocks a bad model promote?  
5. Threat-register “malicious MCP tool description” → ASI02/ASI04 controls.  
6. Define revoke procedure when a model hub or MCP package is compromised (sessions, pins, secrets).  
7. Confirm embedding and model backups inherit classification of their source data.

### Self-check

- [ ] Name AI-specific supply artifacts beyond normal SBOM  
- [ ] Explain AIBOM/ML-BOM vs SBOM  
- [ ] Narrate plant → install → trust descriptors → privileged act  
- [ ] List MCP host/client/server and tool-safety principle  
- [ ] Tie lifecycle stages to release gates  
- [ ] State pin/sign/allowlist as default prod posture  

## Next

- [Authorized AI evaluation](./6_Authorized_AI_Evaluation.md)

## Further reading

- [NIST SP 800-218A — SSDF Community Profile for GenAI](https://csrc.nist.gov/pubs/sp/800/218/a/final)
- [CISA / NCSC — Guidelines for secure AI system development](https://www.cisa.gov/resources-tools/resources/guidelines-secure-ai-system-development)
- [Model Context Protocol — Specification](https://modelcontextprotocol.io/specification/2025-11-25)
- [Model Context Protocol — Security best practices](https://modelcontextprotocol.io/docs/2025-06-18/tutorials/security/security_best_practices)
- [OWASP — MCP Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/MCP_Security_Cheat_Sheet.html)
- [CycloneDX — ML-BOM](https://cyclonedx.org/capabilities/mlbom/)
- [OWASP LLM04 / ASI04 (Top 10 2026 hubs)](https://genai.owasp.org/llm-top-10/)
- [Cloud-Security/3 — Supply chain](../Cloud-Security/3_Supply_Chain.md)
