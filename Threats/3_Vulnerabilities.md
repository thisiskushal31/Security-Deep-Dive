# Vulnerabilities

[← Threats](./README.md)

A **vulnerability** is a weakness that a threat can exploit. Not every finding is equally urgent; severity + exposure + asset value drive priority.

## CVE and scoring literacy

| Term | Meaning |
|------|---------|
| **CVE** | Common Vulnerabilities and Exposures — public ID for a known issue |
| **CVSS** | Numeric severity score (context still required: is the box internet-facing?) |
| **CWE** | Weakness class (e.g. injection) — useful for AppSec patterns |
| **Advisory / GHSA** | Vendor or GitHub-issued notice for a package |

Scanners report CVEs; **you** decide fix order using exposure and business impact ([Foundations/1](../Foundations/1_CIA_And_Risk.md)).

## Where vulns live

| Layer | Examples | Typical finders |
|-------|----------|-----------------|
| **Application** | Injection, broken authZ, SSRF | SAST, DAST, code review, pen test |
| **Dependency** | Vulnerable library versions | SCA (lockfile / SBOM) |
| **OS / runtime** | Unpatched kernel, wrong permissions | Host scanners, patch management |
| **Image / container** | Base image CVEs, secrets in layers | Trivy-class tools in CI |
| **Cloud / K8s misconfig** | Public storage, privileged pods | CSPM, KSPM, IaC scan |
| **Protocol / crypto misuse** | Weak TLS, home-rolled crypto | Config review, [Cryptography](../Cryptography/README.md) |

## Misconfig is a vulnerability

“No CVE” does not mean safe. An open S3 bucket or `0.0.0.0/0` SSH is a vulnerability class of its own. Treat misconfig with the same owner + SLA discipline as CVEs ([Cloud-Security](../Cloud-Security/README.md), Handbook posture notes).

## Delivery home

How vulns are gated in pipelines (SAST/SCA/image/DAST): [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security) — especially the gate chain and AST methods. This page is the **program vocabulary**.

## Next

- [Mitigations](./4_Mitigations.md)
