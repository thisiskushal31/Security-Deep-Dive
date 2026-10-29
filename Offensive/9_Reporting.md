# Reporting

[← Offensive](./README.md)

If it is not written clearly, the engagement did not help the defender. Reports turn lab or customer findings into **action**.

## Minimum report sections

| Section | Content |
|---------|---------|
| Scope / dates | What was tested; RoE reference |
| Executive summary | Business risk in plain language |
| Findings | Title, severity, affected assets, evidence |
| Reproduction | Steps a defender can follow in the lab/staging |
| Impact | CIA / business consequence |
| Remediation | Concrete fix; verify steps |
| Appendix | Tools versions, raw logs as needed |

## Severity

Align with the org’s scale (CVSS + exposure, or their matrix). “Critical” without reachability is noise.

## Purple / engineering handoff

For each finding, note: detection gap, ticket owner, and whether a pipeline gate should have caught it ([Handbook gates](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/4_Security_Gate_Chain.md)).

## Next

- [Labs paths](../Labs/1_Paths.md) · return to [Defensive-Ops](../Defensive-Ops/README.md) for detection improvements
