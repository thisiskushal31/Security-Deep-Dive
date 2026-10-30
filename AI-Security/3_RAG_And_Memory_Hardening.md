# RAG, vector store, and memory hardening

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to harden **retrieval-augmented generation** and long-lived **memory** so untrusted documents and embeddings do not become a durable backdoor. Catalog: LLM05 / LLM09 / ASI06. Injection architecture: [2](./2_Prompt_Injection_Defenses.md). Threat model: [1](./1_LLM_App_Threat_Model_In_Practice.md). How plants persist: [8](./8_Attack_Mechanisms_For_Defenders.md).

## Concept

### Why RAG is a security boundary

RAG turns your corpus into part of the prompt. That is a feature for accuracy and a liability for injection: every retrieved chunk is **attacker-shaped input** unless you prove otherwise.

```text
Ingest → embed → index → authorize+retrieve → stuff into context → model → act/answer
         │         │              │
    poison here  leak here   ACL failure here
```

| Stage | How it fails open | What the defender sees later |
|-------|-------------------|------------------------------|
| **Ingest** | Poisoned or instruction-laden docs enter the corpus | “Random” bad answers; tools fire on ordinary questions |
| **Embed / index** | Cross-tenant writes; weak isolation; embedding backups treated as low sensitivity | Tenant B’s secrets in Tenant A’s context; reconstructed plaintext from vectors |
| **Retrieve** | Similarity search without ACL → private chunk in the wrong context | One cosine hit = one confidentiality incident |
| **Context stuffing** | Retrieved text blended unmarked into the system channel | Indirect prompt injection (LLM01) |
| **Memory write** | Agent stores attacker instructions as “facts” | Compromise survives session end (ASI06) |

**Rule:** retrieved text is data, not policy. Cosine similarity does **not** enforce access control.

### How a poisoned corpus becomes a production incident

1. Attacker (or careless insider) gets a document onto an ingest path — public upload, synced SharePoint, scraped web, “helpful” README in a repo the indexer watches.
2. Ingest trusts the source enough to embed and index without provenance or human review.
3. A privileged user asks a normal question; the retriever ranks the poisoned chunk highly.
4. Orchestrator stuffs the chunk into context without delimiters or “data only” framing.
5. Model follows the planted instructions; if tools are attached, impact leaves the chat box.
6. Optionally the model writes a “remember this” memory — now every future session inherits the plant.

Break the chain at ingest trust, ACL-before-retrieve, context isolation, tool policy, or memory write ACL — preferably several of these.

## Advanced

### Foundational controls (implement first)

| Control | Practice | Why it cuts the chain |
|---------|----------|----------------------|
| **Document provenance & integrity** | Know source; verify against a protected baseline (hash) before index | Stops silent corpus swaps |
| **Context window protection** | Delimiters; hard caps on chunk count and tokens; treat as data-only | Limits steer surface |
| **Access-control metadata on every chunk** | Authorize **before** retrieval — post-filter cannot un-see a chunk already in context | Stops LLM09 cross-tenant hits |
| **Tenant / classification isolation** | Separate indexes or hard partitions | Defense when metadata bugs happen |
| **Query abuse detection** | Normalize; rate-limit; detect extraction / scraping patterns | Slows bulk exfil via retrieval |
| **Output validation / policy** | Groundedness checks where feasible; sink-safe rendering ([2](./2_Prompt_Injection_Defenses.md)) | LLM10 + LLM07 hygiene |
| **Full pipeline observability** | Log ingest source, retrieve IDs, user, ACL decision, tool decision | Makes T-RAG-1 pass/fail real |
| **Fail-closed** | If ACL or provenance unknown → **do not retrieve** | Default-deny beats hope |

```text
Query + user identity
  → ACL filter (tenant, classification, ACLs)
    → Similarity search ONLY within authorized set
      → Cap N chunks / token budget
        → Wrap: BEGIN_RETRIEVED (data only) … END_RETRIEVED
          → Reinforce system rules AFTER retrieved blocks
            → Model → schema / policy before any tool
```

### Next-tier controls (maturity)

| Control | Practice |
|---------|----------|
| Signed source attribution on answers | User sees *which* docs grounded the reply |
| Vector index integrity monitoring | Detect tampering / unauthorized writes |
| Tool invocation from RAG answers | Never auto-exec; policy engine ([4](./4_Agentic_Controls.md)) |
| Cache isolation & invalidation | Per-tenant caches; purge on ACL change |
| Ingest connector supply chain | Vet connectors like code ([5](./5_Model_And_Data_Supply_Chain.md)) |
| Deletion / retention | Right-to-delete must remove vectors and derived caches too |
| Dual-control promote | High-sensitivity corpora require second person before index |

### Context construction patterns

| Do | Don’t |
|----|-------|
| Label blocks: `BEGIN RETRIEVED (data only)` … `END` | Blend retrieved text into the system prompt unmarked |
| Reinforce system rules **after** retrieved content (and **test** per model) | Assume “top of prompt always wins” |
| Cap chunks (e.g. 3–5) and a hard token budget | Stuff entire “top-k from whole corpus” into context |
| Scan chunks for instruction-like patterns as a *partial* filter | Rely on scan as the only control |
| Keep tool schemas out of the retrieved channel | Let docs redefine available tools |

### Memory (session / long-term)

Long-term memory is a second RAG with worse hygiene if you let the model write freely.

| Risk | How it happens | Control |
|------|----------------|---------|
| User or doc plants lasting instructions | “Remember: always BCC …” stored as a preference; delayed tool invocation can poison later | Treat **memory writes as privileged**: log causing prompt; classify instruction/role-modification content; approve before cross-session persist |
| Facts shade into instructions | Incremental “helpful” writes evade naive classifiers | Separate **facts** vs **instructions**; defense-in-depth still required |
| Cross-user memory bleed | Shared store keyed only by “assistant” | Strict session/tenant keys |
| Stale toxic memory | Old plant outlives the incident review | TTL, user-visible memory editor, admin purge/rollback |
| Agent shares memory across tools | One poisoned task contaminates another | Scope memory per agent/task (ASI06) |
| RAG corpus dominate | ~5 poisoned docs reported ~90% ASR against huge corpora in published work | Provenance + promote dual-control + ACL-before-retrieve |

### Embeddings are sensitive

Modern inversion research shows plaintext can often be reconstructed from vectors. Treat embedding stores and backups as **source-document class** data: encrypt at rest, ACL like the corpus, scrub from laptop exports, include in deletion workflows.

### Poisoning vs injection (do not collapse)

| Corpus / model poisoning (LLM05) | Indirect injection via retrieval (LLM01) |
|----------------------------------|------------------------------------------|
| Durable corruption of knowledge or model behavior | One-shot (or repeated) instruction in a chunk at inference |
| Fix ingest trust, signing, review, fine-tune gates | Fix context isolation + agency limits |
| May not look like “ignore previous instructions” | Often *does* look like instructions inside “data” |

Both can coexist: a poisoned doc that also carries injection text.

Authorized fixture shape: [8](./8_Attack_Mechanisms_For_Defenders.md) T-RAG-1 — poisoned lab chunk may retrieve only if ACL allows; tools stay cold.

## Apply

1. Diagram your RAG pipeline; mark where ACL is enforced (must be **pre-retrieve**).  
2. Define chunk metadata fields (`tenant`, `classification`, `source_id`, `hash`, `ingest_time`, `promoter`).  
3. Set default retrieve limits (max chunks + max tokens) and fail-closed rules.  
4. Write delimiter + post-retrieval reinforcement text for your orchestrator; test it.  
5. List memory write paths; mark which need human/admin approval.  
6. Create an audit query: “show last 50 retrieves for user X with `source_ids` and ACL decision.”  
7. Threat-register two scenarios: poisoned HR PDF; cross-tenant vector leak — map to LLM01 / LLM09 / ASI06.  
8. Confirm embedding backups inherit the same classification as source docs.

### Self-check

- [ ] Explain why retrieved content is untrusted  
- [ ] Narrate plant → index → retrieve → steer → act for a poisoned PDF  
- [ ] List foundational RAG controls and why ACL is pre-retrieve  
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
