>>// 

| Customer says / symptom keyword | Time of call | Case | Pattern |
|---|---|---|---|
| Labels missing after check-in / "no labels" / "checking in but nothing happens" / lots checked in but not appearing / warehouse stuck after check-in | 05:00–09:00 | 5.1 | A |
| Lots missing after auction / EDI not processed / EKT files not picked up / "lots from auction not in system" | 04:00–10:00 | 5.2 | A |
| Emails not sending / invoices not going out / outbound messages stuck / "customers not receiving anything" | Any | 5.3 | A |
| Orders not coming in / async invoices not generated / scheduled reports not running / inbound messages stuck | Any | 5.4 | A |
| Labels stopped / print jobs not processing / ALPS stuck / "warehouse can't print" / nothing printing | Any | 5.5 | A |
| Prices missing or wrong on webshop / lots not showing prices / calculation engine stuck / "webshop incomplete" | Any | 5.6 | A |
| Labels processed but nothing comes out of printer / printer unreachable / connection error to printer | Any | 5.7 | B — diagnose & hand off |
| Can't log in / can't get into ABS / session already open / "it says my session is active" / user locked out / can't reconnect | Any | 5.8 | B — diagnose & hand off |
| Need to undo an invoice / invoice was wrong / roll back invoice / "invoice needs to be redone" | Any | 5.9 ⚠️ C | C — data change |
| Webshop empty / lots disappeared from webshop / webshop broken / "buyers see nothing" / JSON error | Any | 5.10 ⚠️ C | C — data change |

**How to use this table:**
Match on intent and meaning, not exact wording. If the customer's description is broadly similar to a row, load that case file. Only escalate to Tier 2 if no row comes close.

**Pattern legend:**
- **A — Service Restart:** A Windows service has stopped. You find it, restart it, verify. You fix it yourself.
- **B — Diagnose and hand off:** You run one diagnostic check to confirm the problem, then route to the right party (DevSecOps, ICT partner, or 24x7.nl). You do not fix it yourself.
- **C — Data Correction:** A data change inside ABS. Difficult or impossible to undo. Requires customer coordination and extra caution. Always read the Pattern C warning in `00-overview.md` before proceeding.

---

## Pattern C warning — inline

Any time cases 5.9 or 5.10 are returned, prepend the following before loading the case file:

> ⚠️ **Pattern C — Data Correction case.** Before proceeding:
> 1. Default to the ABS interface, not the database
> 2. Get verbal confirmation from the customer before any destructive action
> 3. If the agent has never run this procedure before, ask Tier 2 or Martijn to walk through it first

---

## No-match rule

If the symptom does not resemble any row in the table above, tell the agent:

> "This does not match any Tier 1 case. Proceed to escalate to Tier 2 immediately."

Do not attempt a fix.

---

## Ambiguous symptom rules

If the symptom could match more than one case: load the one with the higher probability match given the time of call.

If still ambiguous: tell the agent which two cases might apply and ask one clarifying question to distinguish them.

---

## Training examples

*(Add new confirmed examples here as they are encountered in production. Format: customer says → case matched → outcome)*

| Customer said | Matched to | Outcome |
|---|---|---|
| "can't log in" (FCA, any time) | 5.8 — Hanging Session | Sign off disconnected RDS session → resolved |

\\<<
