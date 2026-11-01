# Wargames and CTF fundamentals

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Wargames and entry CTFs build **CLI fluency, patience, and problem decomposition** with clear level ladders and authorized remote hosts. They complement guided platforms ([4](./4_Guided_Platforms_THM_HTB_Academy.md)) when you want fundamentals without a full vulnerable estate.

## Basic apply

### OverTheWire ladder (start here)

Suggested early order from the project’s own guidance:

```text
Bandit (Linux basics)
    │
    ├── Natas (web) 
    ├── Krypton (crypto puzzles)
    └── Leviathan (RE intro)
            │
            ▼
        Narnia → later binary tracks when ready
```

1. Open the wargame page; note **SSH host and port** for that game.  
2. Connect only to that service; play the level.  
3. Keep a level log (password/flag handling per their rules — do not publish spoilers to public repos).  
4. After Bandit ~10–15 levels, write three Linux skills you reused (perms, finding files, networking basics).

### picoCTF-style events / archives

1. Create an account on the official platform.  
2. Solve several **Easy** problems across categories (web, forensics, crypto, reverse).  
3. Notes: category → trick learned → link to Cryptography / AppSec / RE Tooling as fits.  
4. Practice time-boxing: 30 minutes blind, then research *techniques* (not paste-ready flags).

## Advanced apply

### Category rotation week

| Day | Focus | Output in notes |
|-----|-------|-----------------|
| 1–2 | Linux / Bandit deep levels | Commands you now own |
| 3 | Web (Natas or pico web) | One AppSec class named |
| 4 | Crypto puzzle | Tie to Cryptography folder concept |
| 5 | Forensics CTF | Tool used (Wireshark/strings/…) |
| 6 | Blind rematch | Redo an old level without notes |

### Bridge to “real” labs

| When you can… | Move to… |
|---------------|----------|
| Finish Bandit comfortably | Easy THM / HTB boxes ([5](./5_Unguided_Boxes_And_Pro_Labs.md)) |
| Finish early Natas | PortSwigger Apprentice + Juice Shop ([6](./6_Web_AppSec_Labs.md)) |
| Enjoy RE/pwn wargames | Tooling RE + harder CTF bins (lab only) |

### Competition hygiene

- Play only official competition infra.  
- Do not attack scoring systems or co-competitor machines.  
- After contests, rewrite solutions in your own words if you keep a private archive — still no mass copyright dumps into this public series.

## Next

- [Unguided boxes and Pro Labs](./5_Unguided_Boxes_And_Pro_Labs.md) (if you skipped ahead from web) or [Purple](./11_Purple_Detect_What_You_Did.md)

## Further reading

- [OverTheWire Wargames](https://overthewire.org/wargames/)
- [picoCTF](https://picoctf.org/)
- [NIST — Guide to Cybersecurity Competitions (competitors)](https://www.nist.gov/system/files/documents/2023/09/29/Guide%20for%20Competitors.pdf)
