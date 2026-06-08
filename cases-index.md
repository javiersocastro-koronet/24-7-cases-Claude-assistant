>>// 

| Customer says / symptom keyword | Time of call | Case | Pattern |
|---|---|---|---|
| "no labels after checking in" / "checking in all morning" / "lots not getting labels" | 05:00–09:00 | 5.1 | A |
| "lots not appearing after auction" / "EDI not processed" / "lots missing from system" | 04:00–10:00 | 5.2 | A |
| "emails not sent" / "invoices not going out" / "outbound messages stuck" | Any | 5.3 | A |
| "orders not coming in" / "async invoices not generated" / "inbound messages not processing" | Any | 5.4 | A |
| "labels stopped" / "warehouse stuck" / "print jobs not processing" | Any | 5.5 | A |
| "missing lots on webshop" / "prices not calculating" / "calculation engine stuck" | Any | 5.6 | A |
| "labels processed but nothing prints" / "connection error" / "printer unreachable" | Any | 5.7 | B — diagnose & hand off |
| "session already open" / "can't log back in" / "ABS won't open, session active" | Any | 5.8 | B — diagnose & hand off |
| "need to roll back invoice [N]" / "invoice was generated wrong" | Any | 5.9 ⚠️ C | C — data change |
| "webshop is empty" / "lots disappeared from webshop" / "JSON error on webshop" | Any | 5.10 ⚠️ C | C — data change |

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

If the symptom does not match any row in the table above, tell the agent:

> "This does not match any Tier 1 case. Proceed to escalate to Tier 2 immediately."

Do not attempt a fix.

---

## Ambiguous symptom rules

If the symptom matches more than one case: load the one with the higher probability match given the time of call.

If still ambiguous: tell the agent which two cases might apply and ask one clarifying question to distinguish them.

---

## Training examples

*(Add new confirmed examples here as they are encountered in production. Format: customer says → case matched → outcome)*

\\<<
