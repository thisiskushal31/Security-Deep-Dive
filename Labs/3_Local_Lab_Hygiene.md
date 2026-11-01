# Local lab hygiene

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Self-hosted vulnerable apps and VMs are powerful — and dangerous if they touch the internet or your daily-driver network. Hygiene is the difference between a range and an accidental honeypot on your LAN.

## Basic apply

### Default posture: isolated + localhost

| Control | Do this |
|---------|---------|
| Network | Host-only, internal, or air-gapped lab VLAN — not bridged to home IoT |
| Bind address | Prefer `127.0.0.1` for Docker publishes |
| Snapshots | Take a clean snapshot before first attack |
| Data | No real passwords, customer data, or work SSO in lab VMs |

```text
[Your host]
    │
    ├── Lab hypervisor / Docker bridge (isolated)
    │       ├── Victim app / VM (vulnerable on purpose)
    │       └── Optional attacker VM (Kali/Parrot) 
    │
    └── Daily network / Wi‑Fi  ←── do NOT bridge victims here
```

### Stand up one safe local web target

Use official images; keep ports local. Example pattern (confirm tags on the project page before running):

```bash
# Example shape only — pin versions from upstream docs
docker run --rm -p 127.0.0.1:3000:3000 bkimminich/juice-shop
```

Then open `http://127.0.0.1:3000` from the host. If you need Burp, proxy the browser — still against localhost.

Same idea for WebGoat / DVWA: **never** `-p 0.0.0.0:80:80` on a laptop that leaves café Wi‑Fi.

### VulnHub / imported VMs

1. Import into VirtualBox/VMware on an isolated network mode.  
2. Disable shared folders unless required.  
3. Note the lab IP from the guest — attack only that address.  
4. When finished, power off; do not leave vulnerable VMs running overnight on shared Wi‑Fi.

## Advanced apply

### Segment attacker and victim

| Pattern | Why |
|---------|-----|
| Two VMs: attacker + victim on host-only | Mimics real engagement; fewer “I rooted my host” accidents |
| Snapshot before privilege escalation experiments | Fast reset beats debugging a broken disk |
| Separate browser profile for lab SSO/cookies | Stops session bleed into personal accounts |

### Resource and blast-radius limits

| Lab type | Extra hygiene |
|----------|----------------|
| AD estates (GOAD-class) | Enough RAM/disk; never expose RDP/SMB to the internet — [8](./8_AD_And_Windows_Estates.md) |
| Vulhub CVE stacks | One compose project at a time; tear down after the drill |
| CloudGoat | Dedicated cloud account + budget alarms — [9](./9_Cloud_Vulnerable_By_Design.md) |

### Pre-flight before every local session

```text
[ ] Victim not published to LAN/WAN
[ ] Snapshot / clean volume available
[ ] Scope written in notes (IPs, ports)
[ ] Tooling pointed at lab IPs only
[ ] Tear-down plan (docker compose down / VM off)
```

Fail the pre-flight → fix networking first, then practice.

## Next

- [Practice loop and notes](./2_Practice_Loop_And_Notes.md)

## Further reading

- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [OWASP WebGoat](https://owasp.org/www-project-webgoat/)
- [DVWA](https://github.com/digininja/DVWA)
- [VulnHub](https://www.vulnhub.com/)
- [Vulhub](https://github.com/vulhub/vulhub)
