# Planning and scope

[← Offensive](./README.md)

**Written authorization** defines what you may touch. No ticket, no test — including “just a quick nmap.”

## Rules of engagement (minimum)

| Item | Why |
|------|-----|
| In-scope hosts / apps / accounts | Prevent collateral damage |
| Out-of-scope (prod DR, third parties) | Legal and safety |
| Allowed techniques | e.g. no DoS, no phishing employees without HR |
| Time windows | Avoid business-critical moments |
| Contacts / emergency stop | Who to call if something breaks |
| Data handling | How findings and screenshots are stored |

## Lab vs customer engagement

| Lab | Customer |
|-----|----------|
| You are the authorizing party | Signed RoE / SOW |
| Snapshots and resets are fine | Change control may apply |
| Goal is learning | Goal is risk reduction + report |

## Next

- [OSINT](./3_OSINT.md) only after scope is clear — even in a lab, decide what “the target org” means.
