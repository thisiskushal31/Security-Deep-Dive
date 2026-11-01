# Practice loop and notes

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Hours in rooms without notes produce adrenaline, not a **security engineer**. The loop is: **practice on a popular authorized lab → capture what mattered → connect it to a Deep-Dive folder → (when ready) detect it**. Pick your track in [14](./14_Security_Engineer_Lab_Tracks.md).

## Basic apply

### Run one loop per session

```text
Pick scope ──▶ Attempt ──▶ What worked / failed
                              │
                              ▼
                    One-page note ──▶ Link to Security-Deep-Dive topic
                              │
                              ▼
                    Optional: “how would I detect this?” ([11](./11_Purple_Detect_What_You_Did.md))
```

### Use a fixed note template

Copy this into your personal notes (not necessarily this repo):

```markdown
# YYYY-MM-DD — <platform> — <room/lab name>

- Scope: <IPs / URLs authorized>
- Objective (one line):
- Setup: <AttackBox / VPN / local Docker / …>
- What I tried:
- What worked:
- What failed / rabbit holes:
- Concept link: e.g. Offensive/… or AppSec/… or Defensive-Ops/…
- Detection idea (optional): <log / alert I would want>
- Time spent:
```

### Rules that keep the repo clean

| Do | Do not |
|----|--------|
| Date + room name + your words | Paste full paid writeups or copyrighted Academy text |
| Link to official platform room URLs | Commit flags, exam answers, or leaked premium content |
| Record *your* mistakes | Treat someone else’s spoiler dump as “your” notes |

Installs stay in Tooling; methodology stays in Offensive / Defensive-Ops — Labs only points at paths.

## Advanced apply

### Grade each session on transfer, not flags

| Signal you learned | Signal you only collected dopamine |
|--------------------|------------------------------------|
| You can explain the bug class without the room | You need the exact walkthrough next time |
| You mapped it to an AppSec / Offensive note | Notes are only screenshots of flags |
| You listed one detection or fix | “Rooted” with empty detection box forever |

### Weekly review ritual (30 minutes)

1. Skim the week’s notes; star two techniques worth re-testing blind.  
2. Re-run **one** technique against a different target (new room or local app).  
3. Add one purple line: telemetry that would have caught step X — [11](./11_Purple_Detect_What_You_Did.md).  
4. Update your personal path checkboxes from [12](./12_Path_Map.md).

### Pair offense with a defender question every time

After any successful exploit in a lab:

| Ask | Write one sentence |
|-----|--------------------|
| What log existed? | e.g. web access log with payload |
| What would a detection look like? | e.g. uncommon process chain |
| What control would break this path? | e.g. WAF rule / patch / MFA |

You do not need a full SIEM to practice the *habit*.

## Next

- [Guided platforms (THM / HTB Academy)](./4_Guided_Platforms_THM_HTB_Academy.md)

## Further reading

- [Offensive](../Offensive/README.md) · [Defensive-Ops](../Defensive-Ops/README.md) · [AppSec](../AppSec/README.md)
- [NIST — Cyber Ranges](https://www.nist.gov/system/files/documents/2018/02/13/cyber_ranges.pdf)
