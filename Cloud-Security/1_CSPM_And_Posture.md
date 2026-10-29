# CSPM and posture

[← Cloud-Security](./README.md)

Most cloud breaches are **misconfiguration and identity**, not novel zero-days. CSPM reads the **live account**; IaC scan reads the **diff**.

## Misconfig as the common failure

| Pattern | Why it hurts |
|---------|--------------|
| Public storage / world-open shares | Data disclosure without an exploit |
| `0.0.0.0/0` admin ports | Opportunistic takeover |
| Missing logging / CloudTrail-class audit | Blind IR |
| Over-broad IAM (`*:*` on humans or CI) | Blast radius |
| Root / owner keys in daily use | No accountability |

## CSPM vs IaC vs admission

| Check | Sees | When |
|-------|------|------|
| **IaC policy** (Checkov/OPA) | Planned change | PR |
| **Admission** | Object at apply | Deploy |
| **CSPM** | Account as-is (drift, console clicks) | Continuous |
| **KSPM** | Cluster API state | Continuous |

DevOps posture vs PR: [Handbook Security/2](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/2_Compliance_And_Threat_Mitigation.md).

## Literacy (not a cert dump)

Know for your cloud: where IAM lives, how public exposure is shown, how audit logs are enabled, and who owns each account. Deep product click-paths stay in Handbook `Cloud/` notes.

## Next

- [Secrets and K8s](./2_Secrets_And_K8s.md)
