# Passive vs active and OPSEC

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

OPSEC (operations security) here means: **who learns that you are looking**, and **what your looking reveals about you**. Both testers and defenders care. Quiet-first order: [4](./4_Passive_OSINT_And_Public_Sources.md) → confirm in [5](./5_Internet_Exposure_And_ASM.md) / [6](./6_Network_And_Host_Discovery.md).

## Concept

### Passive

You consume data **already published** or collected by someone else: Certificate Transparency (CT) logs, WHOIS/RDAP, search indexes, archives, threat intel portals, exposure-search engines.

| Pros | Cons |
|------|------|
| Low / zero packets to target | Stale or wrong attribution |
| Harder for target to see *you* | Incomplete liveness |
| Great first pass | Misses internal-only services |

### Active

You **generate** traffic toward the target or its infrastructure: DNS queries from your resolver path, TCP SYN scans, HTTP directory busting, TLS handshakes for fingerprinting.

| Pros | Cons |
|------|------|
| Ground truth (is it up?) | Visible in logs / IDS (Intrusion Detection System) |
| Version / path detail | RoE (Rules of Engagement) required |
| Finds shadow services | Timing mistakes ≈ outage |

```text
Passive first ──▶ hypothesize assets
                      │
                      ▼
              Active confirm (in scope)
                      │
                      ▼
              Prioritize + defend / test
```

### OPSEC both sides

| Role | OPSEC question |
|------|----------------|
| Tester | Does my VPN / VPS / User-Agent burn the engagement? |
| Defender | Can I tell vendor scanners from unknown scanners? |
| Builder | Does my staging hostname leak in CT / DNS before launch? |

## Advanced

### Grey zones (classify honestly)

| Action | Usually classed as | Notes |
|--------|-------------------|--------|
| Query public CT for `%.example.com` | Passive | You hit CT operator, not example’s origin |
| `dig` against authoritative NS for example.com | Active-ish | Target’s DNS sees the querier path |
| Shodan query for your ASN | Passive query of vendor index | Vendor already scanned |
| Your own masscan of a /16 | Active | Loud; RoE critical |
| Visiting careers page logged into personal LinkedIn | Passive-ish | Leaves identity trails |
| Authenticated crawling with a test account | Active + privileged | Separate RoE line item |

### Tester OPSEC (authorized)

| Habit | Why |
|-------|-----|
| Dedicated infra / IP ranges disclosed to blue | Avoid “unknown attacker” pages |
| Consistent naming in tickets | Correlate findings |
| Don’t mix personal browsing identity with scan VPS | Attribution mistakes |
| Respect rate limits | Accidental DoS (denial of service) fails the engagement |
| Separate API keys for engagements | Revocation without collateral |

### Defender OPSEC / anti-recon

| Habit | Why |
|-------|-----|
| Split DNS; don’t publish internal names | Shrinks passive map |
| Generic error pages; no stack traces | Less fingerprint fuel |
| Inventory + CT monitoring | Catch leaked pre-prod names |
| Allowlist known scanners | Reduce alert fatigue; spotlight unknowns |
| Canary hostnames / ports | Detect hostile lookalikes early |

### Noise budgets

Even authorized active recon needs a **noise budget**:

| Technique | Relative noise | When to use |
|-----------|----------------|-------------|
| CT / WHOIS / search | Near zero to target | Always first |
| Careful DNS / HTTP GET | Low | Confirm names |
| Top-ports SYN | Medium | Scoped ranges |
| Full port + `-sV` + scripts | High | Narrow hosts after triage |
| Aggressive timing / huge wordlists | Very high | Often out of RoE on prod |

### Linking to ATT&CK

Passive-heavy techniques cluster under search / gather info; active scanning is T1595. Purple teams should pick **one quiet and one loud** technique per exercise so detections cover both.

## Apply

1. For a domain you own: list three **passive** checks and three **active** checks.  
2. Mark which of the six would appear in *your* logs.  
3. Decide an order that is quiet-first, then confirm.  
4. Write one tester OPSEC habit and one defender anti-recon habit you will actually use.

## Further reading

- [MITRE ATT&CK — Active Scanning (T1595)](https://attack.mitre.org/techniques/T1595/)
- [MITRE ATT&CK — Search Open Technical Databases (T1596)](https://attack.mitre.org/techniques/T1596/)
- [NIST SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final)
- [CISA — Cybersecurity best practices (exposure reduction themes)](https://www.cisa.gov/cybersecurity-best-practices)
