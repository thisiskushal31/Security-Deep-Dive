# Endpoint and system hardening literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Hosts, laptops, servers, and containers are where many controls actually run. This note covers foundation-level **system security** ideas from CSEC / GSEC essentials — not full Windows/Linux admin courses.

Baselines: [6](./6_Assets_Data_States_And_Assurance.md). Cloud posture: [Cloud-Security](../Cloud-Security/README.md). Delivery hardening gates: [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

## Concept

## What “hardening” means

Reduce unnecessary attack surface and enforce least privilege on a system so compromise is harder and noisier.

| Lever | Examples |
|-------|----------|
| Inventory | Know what runs where |
| Baseline | Approved image / CIS-class configuration |
| Patch | Close known vulns on a cadence ([20](./20_Vulnerability_Management_Literacy.md)) |
| Remove / disable | Unused services, accounts, protocols |
| Restrict | Local firewall; app allow lists; USB policy |
| Protect identity | Local admin reduction; LAPS-class secrets; MFA to admin paths |
| Detect | EDR / logging agents forwarding to SIEM |

## OS as a control plane

| Function | Why security cares |
|----------|--------------------|
| Authentication / local accounts | Stolen local admin owns the box |
| Permissions on files and processes | Integrity and confidentiality of data on disk |
| Services and listeners | Every open port is surface |
| Update / package management | Patch pipeline health |
| Logging / audit | Feeds detection and IR |

Windows, Linux, and macOS differ in tools; the jobs are the same.

## Advanced

## Endpoint classes

| Class | Foundation focus |
|-------|------------------|
| User endpoints | Disk encryption, MFA, EDR, quick patch, USB control |
| Servers | Baseline image, least services, admin jump hosts, monitoring |
| Mobile | MDM/MAM policy; separate work data; remote wipe literacy |
| Containers / VMs | Minimal images; no secrets in layers; runtime policy |

## Malicious code mitigation (literacy)

| Control | Role |
|---------|------|
| Application allow listing | Only approved binaries run |
| EDR / AV | Detect and contain known/suspicious behavior |
| Macro / script controls | Reduce common delivery paths |
| Browser / email isolation patterns | Limit drive-by and attachment impact |
| Restore capability | Assume some hosts will need rebuild ([21](./21_Resilience_Continuity_And_Recovery_Literacy.md)) |

Malware family catalogs and reverse engineering are **not** Foundations — see [Threats](../Threats/README.md) and Tooling as needed.

## Apply
1. Start from a minimal approved image.  
2. Change control for baseline exceptions ([9](./9_Change_Management_And_Security.md)).  
3. Prefer remove over “listen on all interfaces and hope.”  
4. Admin through dedicated paths; log privileged actions ([7](./7_AAA_And_Access_Fundamentals.md)).  
5. Treat drift from baseline as a signal.

## Self-check

- [ ] Define hardening in one sentence  
- [ ] List five levers on a laptop and five on a server  
- [ ] Explain why patching without inventory fails  

## Next

- [Human security literacy](./16_Human_Security_Literacy.md)

## Further reading

- [NIST SP 800-123 — Guide to General Server Security](https://csrc.nist.gov/pubs/sp/800/123/final)
- [CIS Benchmarks (overview)](https://www.cisecurity.org/cis-benchmarks)
- [NIST SP 800-40 Rev. 4 — Guide to Enterprise Patch Management Planning](https://csrc.nist.gov/pubs/sp/800/40/r4/final)
