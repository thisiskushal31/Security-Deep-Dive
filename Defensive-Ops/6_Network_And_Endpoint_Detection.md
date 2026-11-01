# Network and endpoint detection

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Network sensors and endpoint agents catch different slices of the same intrusion. Treat them as **roles in a detection fabric**, not competing products. IDPS (Intrusion Detection and Prevention System) classes and deployment issues are long-standing; EDR (Endpoint Detection and Response) adds host process and file visibility. Wire tooling installs: [Tooling Defensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive). Packet theory: [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive).

## Concept

### Who sees what

| Layer | Strong at | Weak at |
|-------|-----------|---------|
| **Network IDS/IPS** | Lateral traffic patterns, known exploit payloads, policy violations on the wire | Encrypted payloads without TLS (Transport Layer Security) inspection policy; host-only actions |
| **NDR (Network Detection and Response)-style analytics** | Behavior on flows / DNS | Endpoint process trees |
| **EDR / endpoint** | Process lineage, file mods, persistence | Blind hosts without agents; some kernel rootkits |
| **Email / web gateway** | Delivery stage | Post-click host activity alone |

```text
Internet / users
       │
       ▼
 edge / email / DNS ──▶ network sensors
       │
       ▼
 hosts with EDR ──▶ process / file / auth on box
       │
       ▼
 SIEM / XDR (Extended Detection and Response) correlation ([3](./3_SIEM_And_Detection_Platforms.md))
```

### Prevention vs detection

IPS (prevention) can block; IDS (detection) alerts. Blocking wrong can cause outages — change control and staging matter. Prefer detect-first for uncertain signatures; prevent when confidence and business impact are clear.

## Advanced

### IDPS deployment classes (timeless)

| Class | Placement idea |
|-------|----------------|
| Network-based | Tap / span / inline on key segments |
| Wireless | Watch WLAN (wireless LAN) threats on corp Wi-Fi |
| NBA (network behavior analysis) | Flow anomalies |
| Host-based | On the endpoint / OS audit |

Encrypted traffic shrinks payload inspection unless you terminate TLS at an approved middlebox with policy — privacy and breakage trade-offs belong to architecture reviews, not silent MITM (man-in-the-middle) experiments.

### EDR realities

| Habit | Why |
|-------|-----|
| Coverage metrics (agents online) | Blind fleets fake security |
| Exclude lists reviewed | Attackers live in exclusions |
| Isolate API tested in lab | Containment button must work under stress ([8](./8_Contain_Eradicate_Recover.md)) |
| Same identity context as IdP (Identity Provider) | User-centric investigations |

### NSM (Network Security Monitoring) logs vs full PCAP

| Artifact | Strength |
|----------|----------|
| Protocol/metadata logs (Zeek-class) | Cheap, long retention, great for hunt pivots |
| IDS alerts (Suricata-class) | Signature / rule hits with context |
| Full PCAP | Ground truth for short windows; expensive |

Lab installs: [Zeek](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Zeek) · [Suricata](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Suricata) · [Wireshark](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wireshark) · [tcpdump](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/tcpdump).

### Endpoint query at fleet scale

When you need “which hosts have this persistence?” across thousands of machines, osquery-class agents complement EDR. Lab: [osquery](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/osquery). Memory deep-dives: [Volatility](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Volatility).

### Email and DNS as first-class sensors

Many intrusions start before EDR sees a process: phish delivery, callback DNS, anomalous MX paths. Treat mail gateway and DNS resolver logs as required telemetry in [2](./2_Telemetry_And_Log_Management.md), not optional extras.

### Avoid duplicate tooling trees

Capture and memory tools answer deep investigation questions; they are not a second SIEM. Install and first use stay in Tooling. This note owns **when** network vs endpoint evidence matters in detection and IR.

## Apply

1. Diagram where your IDS/IPS or NDR sensors sit relative to critical VLANs (virtual LANs) / VPCs.  
2. Report EDR coverage % for laptops and servers separately.  
3. Pick one ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) technique; say whether network, endpoint, or both would see it.  
4. Test (lab) whether isolate / network quarantine works on a non-prod host.  
5. Confirm DNS and mail logs reach your searchable store.

## Further reading

- [NIST SP 800-94 — Guide to Intrusion Detection and Prevention Systems (IDPS)](https://csrc.nist.gov/pubs/sp/800/94/final)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE D3FEND](https://d3fend.mitre.org/)
- [Tooling Security / Defensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive)
