# Vectors and attack surfaces

[← Threats](./README.md)

A **vector** is how an attack arrives. An **attack surface** is everything that can receive that arrival (exposed ports, users, dependencies, admin consoles).

## High-frequency vectors (program view)

| Vector | Surface it hits | First defense grain |
|--------|-----------------|---------------------|
| Phishing / social | Humans, endpoints, IdP | MFA, phishing-resistant auth, training + reporting |
| Stolen or leaked credentials | Apps, VPN, cloud consoles | MFA, short-lived tokens, secret scanning |
| Unpatched software | OS, browsers, libraries, appliances | Patch SLA, SCA in CI |
| Misconfiguration | Cloud buckets, SG/NSG, K8s RBAC | CSPM/KSPM, IaC policy |
| Exposed API / app bug | Public or partner HTTP | AppSec, WAF, authZ tests |
| Supply chain | Build, deps, images, vendor updates | SBOM, signing, pin + review |
| Malware / drive-by | Endpoints | EDR, least privilege, browser isolation |
| Physical / lost device | Laptops, tokens, disks | Disk encryption, remote wipe |

## Network-layer depth lives elsewhere

L2/L3 attacks, spoofing on the wire, TLS failures, firewall placement: [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive) (especially attacks/mitigations and perimeter notes). Do not duplicate packet labs here.

## Shrinking surface

1. Inventory what is reachable from the internet and from each trust zone.  
2. Remove or hide what you do not need (ports, admin UIs, debug endpoints).  
3. Authenticate and authorize every remaining entry.  
4. Monitor the rest ([Defensive-Ops](../Defensive-Ops/README.md)).

## Next

- [Vulnerabilities](./3_Vulnerabilities.md)
