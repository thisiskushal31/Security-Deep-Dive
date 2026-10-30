# RAG, vector store, and memory hardening

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to harden **retrieval-augmented generation** and long-lived **memory** so untrusted documents and embeddings do not become a backdoor. Catalog: LLM05 / LLM09 / ASI06. Injection patterns: [2](./2_Prompt_Injection_Defenses.md). Threat model: [1](./1_LLM_App_Threat_Model_In_Practice.md).

Primary practice source: [OWASP RAG Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/RAG_Security_Cheat_Sheet.html) + OWASP LLM09.

## Concept

### Why RAG is a security boundary

```text
Ingest → embed → index → retrieve → stuff into context → model → act/answer
```

Every stage can fail open:

| Stage | Failure |
|-------|---------|
| Ingest | Poisoned or malicious docs enter the corpus |
| Embed / index | Cross-tenant leakage; embedding inversion risk (LLM02/09) |
| Retrieve | Wrong ACL → private chunk in another user’s context |
| Context stuffing | Indirect prompt injection (LLM01) |
| Memory write | Poison persists across sessions (ASI06) |

**Rule:** retrieved text is **attacker-shaped input**, not trusted policy.

### Foundational controls (implement first — OWASP RAG CS)

| Control | Practice |
|---------|----------|
| **Document provenance & integrity** | Know source; verify against a protected baseline before index |
| **Context window protection** | Delimiters; chunk count/size limits; treat as data-only |
| **Access-control metadata on every chunk** | Authorize **before** retrieval — post-filter cannot un-see a chunk |
| **Tenant / classification isolation** | Separate indexes or hard partitions; no “cosine ignores ACL” |
| **Query abuse detection** | Normalize; rate-limit; detect extraction patterns |
| **Output validation / policy** | Groundedness checks where feasible; sink-safe rendering ([2](./2_Prompt_Injection_Defenses.md)) |
| **Full pipeline observability** | Log ingest, retrieve IDs, user, decision |
| **Fail-closed** | If ACL/provenance unknown → do not retrieve |

## Advanced

### Next-tier controls (compliance / maturity)

| Control | Practice |
|---------|----------|
| Signed source attribution on answers | User sees *which* docs grounded the reply |
| Vector index integrity monitoring | Detect tampering / unauthorized writes |
| Tool invocation from RAG answers | Never auto-exec; policy engine ([4](./4_Agentic_Controls.md)) |
| Cache isolation & invalidation | Per-tenant caches; purge on ACL change |
| Ingest connector supply chain | Vet connectors ([5](./5_Model_And_Data_Supply_Chain.md)) |
| Deletion / retention | Right-to-delete must remove vectors too |

### Context construction patterns

| Do | Don’t |
|----|-------|
| Label blocks: `BEGIN RETRIEVED (data only)` … `END` | Blend retrieved text into system prompt unmarked |
| Reinforce system rules **after** retrieved content (test per model) | Assume “top of prompt always wins” |
| Cap chunks (e.g. 3–5; token budget) | Stuff entire corpus into context |
| Scan chunks for instruction-like patterns as a *partial* filter | Rely on scan as the only control |

### Memory (session / long-term)

| Risk | Control |
|------|---------|
| User or doc plants lasting instructions | Memory writes require policy; separate “facts” vs “instructions” |
| Cross-user memory bleed | Strict session/tenant keys |
| Stale toxic memory | TTL, user-visible memory editor, admin purge |
| Agent shares memory across tools | Scope memory per agent/task; ASI06 |

### Embeddings are sensitive

OWASP notes modern inversion can reconstruct plaintext from vectors — treat embeddings backups as **source-document class** data. Cosine similarity does **not** enforce ACLs: **authorize then retrieve**.

### Poisoning vs injection

| LLM05 / corpus poisoning | LLM01 via retrieval |
|--------------------------|---------------------|
| Durable corruption of knowledge/model behavior | One-shot (or repeated) instruction in a chunk at inference |
| Fix ingest trust, signing, review | Fix context isolation + agency limits |

How attackers plant RAG/memory instructions and what authorized fixtures look like: [8](./8_Attack_Mechanisms_For_Defenders.md) (RAG poison + T-RAG-1).

## Apply

1. Diagram your RAG pipeline; mark where ACL is enforced (must be pre-retrieve).  
2. Define chunk metadata fields (tenant, classification, source_id, hash, ingest_time).  
3. Set default retrieve limits (max chunks + max tokens) and fail-closed rules.  
4. Write delimiter + post-retrieval reinforcement text for your orchestrator.  
5. List memory write paths; mark which need human/admin approval.  
6. Create an audit query: “show last 50 retrieves for user X with source_ids.”  
7. Threat-register two scenarios: poisoned HR PDF; cross-tenant vector leak — map to LLM01/09/ASI06.

### Self-check

- [ ] Explain why retrieved content is untrusted  
- [ ] List foundational RAG controls (OWASP priority set)  
- [ ] State authorize-before-retrieve  
- [ ] Distinguish corpus poisoning vs indirect injection  
- [ ] Name memory poisoning controls (ASI06)  
- [ ] Treat embeddings as sensitive data  

## Next

- [Agentic controls](./4_Agentic_Controls.md)

## Further reading

- [OWASP — RAG Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/RAG_Security_Cheat_Sheet.html)
- [OWASP LLM09 — Vector and Embedding Weaknesses (Top 10 2026)](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [OWASP ASI06 — Memory & Context Poisoning](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [NIST AI 600-1 — Generative AI Profile](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
