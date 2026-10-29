# Controls and physical security

[← Foundations](./README.md)

Controls are how you reduce risk. Classify them so you do not pretend a lock replaces logging — or a camera replaces access policy.

## By nature (common Security+ framing)

| Type | What it is | Example |
|------|------------|---------|
| **Technical** | Hardware/software enforcement | MFA, encryption, WAF, EDR |
| **Managerial** (administrative) | Policy and process | Access reviews, change control, hiring checks |
| **Operational** | Day-to-day people procedures | Patch cadence, backup restore drills, on-call IR |
| **Physical** | Buildings and media | Badges, locks, cages, secure disposal |

## By function

| Function | Job | Example |
|----------|-----|---------|
| **Preventive** | Stop the bad event | Least privilege, input validation, network segmentation |
| **Detective** | Notice it happened or is happening | SIEM alerts, audit logs, IDS |
| **Corrective** | Limit damage / restore | Incident response, restore from backup, revoke keys |
| **Deterrent** | Discourage attempt | Warnings, visible cameras (weak alone) |
| **Compensating** | Alternate control when primary is hard | Extra monitoring when legacy cannot be patched yet |

A healthy program mixes **preventive + detective + corrective**. Detection without response is a dashboard hobby.

## Physical — literacy only

| Control | Why it exists |
|---------|----------------|
| Locks, badges, visitors | Stop walk-up access to racks and desks |
| CCTV / guards | Detect and deter; not a substitute for IAM |
| Faraday / TEMPEST-class ideas | Limit RF leakage in high-assurance settings — rare for most product teams |
| Media destruction | Disks and tapes leave buildings; wipe or shred on policy |

Datacenter bring-up and rack practice: DevOps-Handbook / Containerization as relevant. This note only so you **do not ignore** the building when you secure the cluster.

## Related home

- Controls on the wire → [Networks Security](https://github.com/thisiskushal31/Networks-Deep-Dive)
- Pipeline and secrets controls → [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security)
- Detection and IR → [Defensive-Ops](../Defensive-Ops/README.md)

## Next

- [Threats](../Threats/README.md)
