# Model confidentiality and classical adversarial ML

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How **training-data privacy**, **model IP**, **inference APIs**, and **classical adversarial ML** fail — and how to operate models safely. Complements GenAI injection/agency notes [2](./2_Prompt_Injection_Defenses.md)–[4](./4_Agentic_Controls.md) and supply chain [5](./5_Model_And_Data_Supply_Chain.md). Taxonomy home: [NIST AI 100-2e2025](https://csrc.nist.gov/pubs/ai/100/2/e2025/final). Ops checklist: [OWASP Secure AI Model Ops](https://cheatsheetseries.owasp.org/cheatsheets/Secure_AI_Model_Ops_Cheat_Sheet.html).

Prompt injection is not the only AI attack class. Predictive and generative systems also lose **confidentiality** (who was in the train set; what the model weights are) and **integrity** (adversarial examples; poisoned train data — [3](./3_RAG_And_Memory_Hardening.md), [5](./5_Model_And_Data_Supply_Chain.md)).

```text
Train / fine-tune data ──▶ Model artifact ──▶ Registry / store ──▶ Inference API ──▶ Clients
        │                      │                    │                  │
   membership /            extraction /         open bucket /     scraping /
   inversion risk          unsafe deserialize    weak IAM         DoW / abuse
```

## Concept

### Confidentiality attacks (what the adversary learns)

| Attack | Goal | Why it matters |
|--------|------|----------------|
| **Membership inference** | Decide whether a specific record was in the training set | Privacy / regulatory exposure (health, finance, HR) |
| **Model inversion / reconstruction** | Recover approximate training inputs or sensitive attributes from the model | Training-data leakage without stealing the file |
| **Model extraction / theft** | Recreate a functional equivalent via API querying, or steal weights from a registry | IP loss; shadow models; bypass of paid APIs |
| **Property inference** | Learn global properties of the training distribution | Competitive / privacy signal |

These sit under NIST AML privacy / GenAI misuse families. They are **not** fixed by prompt filters.

### Integrity attacks (classical PredAI)

| Attack | Goal | Defender note |
|--------|------|---------------|
| **Evasion / adversarial examples** | Tiny input changes flip the prediction (stop sign → speed limit class) | Test with adversarial suites; confidence / OOD monitors |
| **Poisoning / backdoors** | Contaminate train or fine-tune data so behavior flips on a trigger | Ingest integrity — [5](./5_Model_And_Data_Supply_Chain.md) |
| **Energy-latency / sponge** | Inputs that burn compute | Quotas — [4](./4_Agentic_Controls.md) LLM06 |

### How extraction usually happens in production

1. Attacker finds an **unauthenticated or weakly rate-limited** inference endpoint (or an orphaned staging model left public).  
2. They query systematically (many inputs, sometimes adaptive) and collect outputs.  
3. They train a **shadow model** that approximates yours — or probe until membership/inversion succeeds on sensitive classes.  
4. Separately: misconfigured object storage / MLflow / registry grants direct download of weights (classic theft, not query extraction).

Break the chain with authn/authZ, rate limits, anomaly detection on scrape-like patterns, registry IAM, and encryption at rest.

## Advanced

### Model artifact and registry hygiene

| Control | Practice |
|---------|----------|
| Central model registry | One inventory; RBAC; no “model.zip on a public bucket” |
| Sign + pin artifacts | Same posture as [5](./5_Model_And_Data_Supply_Chain.md) |
| Encrypt weights and datasets at rest | Treat as source-document class when trained on sensitive data |
| Restrict training logs / intermediates | Logs often contain samples or PII |
| Validate third-party models before promote | Provenance + malware scan |

### Unsafe model serialization (RCE-class)

Some formats execute code on load (classic **pickle** / unsafe `torch.load` patterns). A malicious `.pkl` / `.pt` uploaded into a pipeline can become **host compromise**, not “just ML.”

| Do | Don’t |
|----|-------|
| Prefer safe formats / weights-only loads where the stack supports them | Blind `pickle.load` / unrestricted deserialize from the internet |
| Scan and sandbox untrusted model evaluation jobs | Open arbitrary community checkpoints on a prod GPU host with secrets mounted |
| Isolate conversion / merge workers with no egress to secrets | Run untrusted fine-tunes on the same node as prod inference |

### Inference API security

| Control | Practice |
|---------|----------|
| Authn / authZ on every endpoint | No anonymous prod inference |
| Rate limits + concurrency + spend caps | Extraction and denial-of-wallet |
| Input validation | Size, schema, modality limits |
| Abuse / scrape detection | Alert on high-volume diverse queries characteristic of extraction |
| Per-tenant quotas | Multi-tenant isolation |
| Circuit breakers / kill switch | Abnormal cost, latency, or tool-call spikes — [4](./4_Agentic_Controls.md) |

### Runtime and hardware isolation

Shared GPUs and accelerators can leak across tenants (memory residue, side channels) when isolation is weak.

| Control | Practice |
|---------|----------|
| Separate train / eval / prod by trust boundary | Do not mix untrusted fine-tunes with prod serving |
| Clear accelerator memory / caches between jobs when the runtime allows | Reduce cross-job residue |
| Sandbox untrusted model eval | Restricted egress; no host mounts; no cloud metadata |
| Consider microVMs / confidential compute for high-sensitivity models | When classification demands it |
| Tear down artifacts on job end | Checkpoints, prompt logs, cached embeddings |

### Privacy engineering levers

| Lever | When |
|-------|------|
| Minimize sensitive fields in train/fine-tune sets | Default |
| Anonymization / synthetic data (with care — re-identification risk remains) | Regulated domains |
| Differential privacy (e.g. DP-SGD class techniques) where accuracy tradeoff is accepted | Strong privacy requirement |
| Strict access to fine-tune APIs | Loss oracles can help optimize attacks against closed models ([8](./8_Attack_Mechanisms_For_Defenders.md)) |

### Monitoring, drift, and AI incident handoff

| Signal | Action |
|--------|--------|
| Input distribution / output entropy / latency shifts | Investigate drift or abuse |
| Scrape-like query patterns | Throttle; ticket extraction risk |
| Sudden token/$ spikes | Budget trip; kill switch |
| Model abuse or poisoning suspicion | Rollback to last signed good artifact |

Define escalation and rollback in the IR plan. SIEM depth and playbooks live in [Defensive-Ops](../Defensive-Ops/README.md) — wire AI-specific signals into that home; do not reinvent a second SOC here.

### Common Model Ops failure modes (checklist)

| Failure | Fix |
|---------|-----|
| Hardcoded API keys in notebooks | Secret manager; scan repos |
| Open MLflow / registry | Authn + network policy |
| Orphaned staging endpoints | Inventory + decommission |
| Unvalidated third-party checkpoints | Pin/sign/scan before promote |
| No monitoring | Add abuse + drift + cost alerts |

## Apply

1. Inventory every inference endpoint (prod + staging); mark authn and rate limits.  
2. Confirm model registry IAM; close any public artifact URLs.  
3. Document deserialize path for your stack (safe load vs pickle risk).  
4. Add scrape/extraction detection heuristic to the monitoring backlog.  
5. Threat-register membership inference for one sensitive train set; pick a privacy lever.  
6. Write an AI rollback step into the IR runbook (signed artifact ID + kill switch).  
7. Link three tickets to Defensive-Ops detection owners.

### Self-check

- [ ] Distinguish membership inference, inversion, and extraction/theft  
- [ ] Explain query-based extraction vs registry theft  
- [ ] Name unsafe deserialize risk for model files  
- [ ] List inference API controls (authn, rate limit, abuse detect)  
- [ ] State GPU/tenant isolation habits  
- [ ] Know where AI IR/monitoring hands off to Defensive-Ops  

## Further reading

- [NIST AI 100-2e2025 — Adversarial Machine Learning](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)
- [OWASP — Secure AI Model Ops Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secure_AI_Model_Ops_Cheat_Sheet.html)
- [OWASP — AI Security Verification Standard (AISVS)](https://genai.owasp.org/resource/owasp-ai-security-verification-standard/)
- [NIST SP 800-218A — SSDF for GenAI](https://csrc.nist.gov/pubs/sp/800/218/a/final)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [Defensive-Ops](../Defensive-Ops/README.md)
- [Model and data supply chain](./5_Model_And_Data_Supply_Chain.md)
