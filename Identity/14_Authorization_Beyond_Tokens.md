# Authorization beyond tokens

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

OAuth **scopes** and group claims answer coarse “what class of access?” Fine-grained **authorization** (who may do *this* to *that* object) usually needs a second decision layer. Confusing “valid token” with “allowed action” is how broken access control ships.

## Concept

### Authentication vs authorization (again, with teeth)

| Question | Layer |
|----------|-------|
| Who is this? | Authn — IdP / authenticator — [1](./1_Digital_Identity_And_Assurance_Levels.md)–[3](./3_Authenticators_MFA_And_WebAuthn.md) |
| May this principal perform this action on this resource *now*? | AuthZ — policy engine / app logic |

A correct JWT signature only proves the token is well-formed and trusted. It does not prove the operation is allowed.

### Common authZ models

| Model | Idea | Typical fit |
|-------|------|-------------|
| **RBAC (role-based)** | Roles → permissions | Coarse enterprise apps |
| **ABAC (attribute-based)** | Attributes of user, resource, env → decision | Dynamic / contextual access |
| **ReBAC (relationship-based)** | Graph of relationships (“owner of”, “member of”) | Collaborative / multi-tenant SaaS |
| **ACL** | Per-object principal lists | Simple resources; scales poorly alone |

OAuth scopes often *encode* coarse RBAC (“`admin:write`”). They rarely replace object-level checks.

```text
Request + token claims ──▶ Policy decision point (PDP)
                                │
                                ▼
                         allow / deny / obligations
                                │
                                ▼
                         Policy enforcement point (PEP)
```

## Advanced

### Where policy lives

| Pattern | Trade-off |
|---------|-----------|
| **In-app `if` statements** | Fast to start; drifts; hard to audit |
| **Central PDP** (OPA, Cedar, Keto, …) | Consistent decisions; new ops surface |
| **IdP as only authZ** | Convenient; IdP becomes god-object; poor object granularity |
| **Gateway-only authZ** | Good for coarse routes; insufficient for row-level rules |

Zero Trust policy engines: [9](./9_Zero_Trust_Architecture.md). Tool installs: [Identity tooling map](./16_Identity_Tooling_Map.md).

### Tokens should carry *inputs*, not infinite power

| Prefer in token / claims | Prefer in PDP / data plane |
|--------------------------|----------------------------|
| Stable subject ID, tenant ID | “May edit document 123?” |
| Coarse roles / scopes | Field-level / row-level rules |
| ACR / amr (auth strength) | Step-up obligations |

Minting a JWT that embeds every permission for every object creates giant tokens and revocation pain — [8](./8_Tokens_Sessions_And_Client_Patterns.md).

### Policy-as-code habits

| Habit | Why |
|-------|-----|
| Version policies; review like code | AuthZ bugs are CVEs |
| Test allow *and* deny cases | Broken access is silent |
| Log decision id + inputs (redacted) | Forensics / ITDR handoff — [12](./12_Identity_Threats_Detect_And_Harden.md) |
| Separate “break-glass” policies | Standing admin roles are PAM’s problem — [15](./15_Privileged_Access_And_IGA.md) |

### Standards-adjacent names you will hear

| Name | Grain |
|------|-------|
| **OPA (Open Policy Agent)** + Rego | General PDP; sidecar/gateway/app |
| **Cedar** (Amazon) | Policy language aimed at app authZ |
| **Ory Keto** | Relationship/permission APIs (Zanzibar-inspired) |
| **XACML** | Older enterprise ABAC standard (heavy) |
| **ID-token / UserInfo claims** | Inputs to PDP — not a PDP by themselves |

AppSec broken-access patterns: [AppSec](../AppSec/README.md).

## Apply

1. Pick one sensitive API; list checks that are “token valid” vs “object authZ.”  
2. Name your authZ model (RBAC / ABAC / ReBAC / mix).  
3. Move one hard-coded permission check into a reviewed policy artifact.  
4. Add deny tests for cross-tenant access.  
5. Ensure authZ denials are visible to detection (not only 403s with empty logs).

## Next

- [Privileged access and IGA](./15_Privileged_Access_And_IGA.md)

## Further reading

- [Open Policy Agent docs](https://www.openpolicyagent.org/docs/latest/)
- [Cedar language guide](https://docs.cedarpolicy.com/policy/syntax-grammar.html)
- [Ory Keto documentation](https://www.ory.sh/docs/keto)
- [OWASP Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
