# Unguided boxes and Pro Labs

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Unguided targets give a machine (or small network) and little hand-holding. Use them **after** guided paths ([4](./4_Guided_Platforms_THM_HTB_Academy.md)) so enumeration habits exist before frustration becomes quitting.

## Basic apply

### Easy box loop (HTB-style or similar)

```text
Scope IP ──▶ Recon (ports/services) ──▶ Enumerate ──▶ Initial access
                                                      │
                                                      ▼
                                              Privesc ──▶ Notes + reset mindset
```

1. Confirm VPN / lab access and **only** the assigned IP.  
2. Run intentional recon (Nmap in Tooling) — save output into notes.  
3. Enumerate the promising service; avoid spray-and-pray across the whole platform.  
4. When stuck 25–40 minutes, write the hypothesis, then use a hint/community nudge per platform rules.  
5. After root/user flags: document the **shortest** real path, not every dead end.

### VulnHub / offline VMs

1. Download a beginner VM from an official project page.  
2. Import on isolated networking ([3](./3_Local_Lab_Hygiene.md)).  
3. Treat DHCP/host-only IP as sole scope.  
4. Same note template as guided rooms ([2](./2_Practice_Loop_And_Notes.md)).

### Success criteria for “easy”

| Pass | Retry later |
|------|-------------|
| You rooted with notes that explain *why* | You only followed a spoiler line-by-line |
| You can restate the foothold in one sentence | You cannot find the foothold without the writeup open |

## Advanced apply

### Chain skills across boxes

| Focus week | Box selection habit |
|------------|---------------------|
| Web foothold | Filter for HTTP-heavy easy/medium |
| Privesc | Linux then Windows user→root paths |
| AD intro | Only after single-host comfort — [8](./8_AD_And_Windows_Estates.md) |

### Pro Labs / multi-host ranges

Enterprise-style labs (HTB Pro Labs and peers) simulate networks: pivot, cred reuse, AD. Before joining:

1. Finish multiple medium single boxes.  
2. Comfortable with note-taking under time pressure.  
3. Clear calendar — these are campaigns, not one-hour rooms.  
4. Still attack **only** in-lab networks; no “extra” scanning.

```text
Entry host ──pivot──▶ Internal segment ──▶ Domain targets
         │                      │
         └── document every cred / share / ticket you find
```

### Anti-patterns

| Anti-pattern | Fix |
|--------------|-----|
| Spoiler-first every machine | Blind attempt timer before writeups |
| Tool spaghetti without recon notes | Force a recon section before exploits |
| Rage-reset without learning capture | Write “why I reset” before reverting |

## Next

- [Blue, SOC, and DFIR labs](./7_Blue_SOC_And_DFIR_Labs.md)

## Further reading

- [Hack The Box](https://www.hackthebox.com/)
- [HTB Pro Labs](https://www.hackthebox.com/hacker/pro-labs)
- [VulnHub](https://www.vulnhub.com/)
- [Metasploitable 3](https://github.com/rapid7/metasploitable3)
