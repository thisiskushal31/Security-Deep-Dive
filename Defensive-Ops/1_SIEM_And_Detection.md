# SIEM and detection

[← Defensive-Ops](./README.md)

A **SIEM** (Security Information and Event Management) aggregates logs and alerts so humans can detect and investigate abuse. Buying a SIEM without sources, use cases, and on-call is a dashboard.

## What it is for

| Job | Practice |
|-----|----------|
| Collect | Auth, endpoint, cloud audit, network, app, email |
| Normalize / parse | Same fields for “user”, “src_ip”, “action” |
| Detect | Rules, UEBA, threat intel matches |
| Investigate | Pivot timelines; enrich with asset ownership |
| Report | Metrics: MTTD, coverage vs ATT&CK |

Packet capture literacy: [Tooling Wireshark/tcpdump](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive). Network blue-team notes: [Networks Security](https://github.com/thisiskushal31/Networks-Deep-Dive).

## ATT&CK as a coverage map

Map detections to [MITRE ATT&CK](https://attack.mitre.org/) techniques ([GRC](../GRC/1_NIST_ISO_And_ATTCK.md)). Ask: which techniques matter for *our* assets, and which have zero telemetry?

## Detection quality

| Better | Worse |
|--------|-------|
| High-fidelity + owner + runbook | Alert spam nobody acknowledges |
| Tested with purple/red exercises | Rules never validated |
| Tuned suppressions with expiry | Permanent mute of noisy truth |

Observability stacks for *product* metrics are adjacent but not SIEM: Handbook Monitoring. Security detections need **security-relevant** telemetry and response paths ([2 Incident response](./2_Incident_Response.md)).

## Related home

- Pipeline gates are not SIEM → DevOps-Handbook `Security/`
- Design monitoring patterns → System-Design Observability (security monitoring note)
