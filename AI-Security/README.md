# AI security (practice deep dive)

[← README](../README.md)

**Status: stub** — literacy lives in [Threats/8](../Threats/8_AI_Era_Threats.md) (DHS/CISA three risk categories, NIST AI 600-1 + AML, OWASP LLM + Agentic Top 10, CCCS pillars, ENISA landscape notes, ATLAS pointer).

This folder is for **build/operate depth** beyond Threats literacy: securing LLM apps and agents, RAG/vector stores, MCP/tool supply chain, AIBOM, authorized AI red-team process, and mappings to NIST AI RMF / SSDF GenAI profile / CISA–NCSC secure AI lifecycle.

Do **not** put jailbreak recipes or unauthorized attack procedures here. Lab work stays authorized and isolated — same ethics boundary as [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

## Planned topic files

| # | Topic | Status |
|---|--------|--------|
| 1 | LLM application threat model (component vs agent) | planned |
| 2 | Prompt injection defenses (architecture, not payloads) | planned |
| 3 | RAG / vector store / memory hardening | planned |
| 4 | Agentic controls (tools, identity, human gates, kill switch) | planned |
| 5 | Model and data supply chain (SBOM/AIBOM, signing, MCP) | planned |
| 6 | Authorized AI red team / evaluation process | planned |
| 7 | Shadow AI governance and private GenAI deployment | planned |

## Related homes (do not duplicate)

| Topic | Home |
|-------|------|
| AI-era threat literacy | [Threats/8](../Threats/8_AI_Era_Threats.md) |
| Classic AppSec / OWASP web | [AppSec](../AppSec/README.md) |
| Supply chain (general) | [Cloud-Security/3](../Cloud-Security/3_Supply_Chain.md) |
| Human / social engineering | [Foundations/16](../Foundations/16_Human_Security_Literacy.md) |
| ATT&CK / ATLAS nesting | [GRC](../GRC/1_NIST_ISO_And_ATTCK.md) · [atlas.mitre.org](https://atlas.mitre.org/) |

## When to fill

Fill when you are shipping or securing LLM/agent products and Threats/8 literacy is already done — not before.
