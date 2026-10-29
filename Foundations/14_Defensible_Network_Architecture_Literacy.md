# Defensible network architecture literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Foundations needs the **security shape** of networks — not packet formats or routing labs. Those live in [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive). This note is the vocabulary GSEC/Security+ expect before firewall and VPN product detail.

Zero Trust and PEPs: [13](./13_Zero_Trust_And_Deception_Basics.md). Scanning ethics: [3](./3_Ethics_Authorization_And_Lab_Rules.md).

## Concept

## Why network architecture matters to security

| Goal | Network idea |
|------|----------------|
| Shrink blast radius | Segmentation and least connectivity |
| Enforce policy at chokepoints | Firewalls, gateways, proxies |
| See abuse | Logging, IDS/IPS, flow telemetry |
| Protect data in motion | TLS / VPN / authenticated channels ([8](./8_Cryptographic_Solutions_Literacy.md)) |

```text
Internet / partners
        │
   Edge / firewall / WAF
        │
   DMZ / public apps
        │
   Internal segments (app / data / admin / OT as needed)
        │
   Egress control + monitoring
```

## Building blocks (literacy)

| Block | Role |
|-------|------|
| Segment / VLAN / VPC subnet | Group systems that share trust and policy |
| Firewall | Allow/deny by policy at a boundary |
| WAF | Application-layer protections for HTTP apps |
| Proxy / egress filter | Control and inspect outbound connections |
| IDS / IPS | Detect (and optionally block) suspicious traffic patterns |
| VPN / ZTNA | Remote access without flat “inside = trusted” |
| Load balancer / API gateway | Often the first trust boundary for apps |
| DNS security | Reduce abuse of resolution and known-bad domains |

## Advanced

## Segmentation habits

| Habit | Why |
|-------|-----|
| Separate admin plane from user plane | Stolen laptop ≠ domain admin path |
| Isolate high-value data stores | Breach of a front-end should not equal DB access |
| Limit east-west by default | Flat networks turn one foothold into many |
| Document intended flows | Unknown flows become detective findings |

## Common failure modes

| Failure | Better |
|---------|--------|
| Flat “inside” trust | Authenticate and authorize per resource ([13](./13_Zero_Trust_And_Deception_Basics.md)) |
| Any→any firewall rules | Least connectivity; review exceptions |
| No egress control | C2 and exfil become free |
| Logging only on the edge | East-west and identity logs matter too |

## Where depth lives

| Topic | Home |
|-------|------|
| TCP/IP, TLS handshakes, Wireshark | [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive) |
| Cloud network posture | [Cloud-Security](../Cloud-Security/README.md) |
| Host firewall / OS controls | [15](./15_Endpoint_And_System_Hardening_Literacy.md) |
| Nmap practice (lab only) | [Tooling Security / Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) |

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Sketch internet → edge → DMZ → internal → data with two trust boundaries  
- [ ] Explain segmentation vs “one big LAN”  
- [ ] Name firewall, IDS/IPS, proxy, and VPN roles without mixing them up  

## Next

- [Endpoint and system hardening literacy](./15_Endpoint_And_System_Hardening_Literacy.md)

## Further reading

- [NIST SP 800-41 Rev. 1 — Guidelines on Firewalls and Firewall Policy](https://csrc.nist.gov/pubs/sp/800/41/r1/final)
- [NIST SP 800-215 — Guide to a Secure Enterprise Network Landscape](https://csrc.nist.gov/pubs/sp/800/215/final)
- [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive/tree/main/Security)
