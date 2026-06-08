>>// 

# 24/7 First-Line Support — Knowledge Repo

This repo enables Claude to act as a live support assistant for Axerrio's 24/7 first-line CS agents. When an agent brings a ticket or call description, Claude triages the issue, identifies the customer via Confluence, matches the symptom to a known case, and walks the agent through resolution step by step.

---

## How to use this repo

- **Always load `00-overview.md`** at the start of every session.
- **For every incoming ticket or call:** read the symptom lookup table at the top of `cases-index.md` to classify the issue. The table is always the first section — read only as far as needed to find a match, then stop. Do not load the full file for every ticket.
- **Once the case is identified:** load the specific `case-5.X-*.md` file that matches. Do not load all case files — load only the one that matches.
- **For customer server information:** query Confluence at https://vertical.atlassian.net/wiki/spaces/A/pages/6321869774/ABS+Services+locations+customers. Look for the columns: ABS Windows Services, EDI Processing, ABC Outbound, ABC Inbound, Remote App, RDS/RA server.
- **If no case matches:** tell the agent to escalate to Tier 2 immediately.

---

## File manifest

| File | What it contains | When to load |
|---|---|---|
| `00-overview.md` | Workflow, triage state machine, resolution patterns, Confluence reference, escalation rules, language rule, PENDING flag rule | Always — load at the start of every session |
| `cases-index.md` | Symptom lookup table (top section), pattern legend, no-match rule, ambiguous symptom rules, training examples | For every ticket — read the top table only, stop when a match is found |
| `case-5.1-batchwise-checkin.md` | Batchwise Check-in service restart procedure | When symptom matches: "no labels after checking in", lots missing after check-in |
| `case-5.2-edi-processing.md` | EDI Processing service restart procedure | When symptom matches: "lots not appearing after auction", "EDI not processed" |
| `case-5.3-abc-outbound.md` | ABC Outbound service restart procedure | When symptom matches: "emails not sent", "invoices not going out" |
| `case-5.4-abc-inbound.md` | ABC Inbound service restart procedure | When symptom matches: "orders not coming in", "async invoices not generated" |
| `case-5.5-alps-labels.md` | ALPS label printing service restart procedure | When symptom matches: "labels stopped", "warehouse stuck", "print jobs not processing" |
| `case-5.6-calc-engines.md` | Calculation Engines restart procedure | When symptom matches: "prices not calculating", "missing lots on webshop", "calculation engine stuck" |
| `case-5.7-connection-problems.md` | Printer connectivity diagnostic and routing | When ALPS is running (jobs status 1) but nothing prints after Print Spooler restart |
| `case-5.8-hanging-session.md` | Hanging RDS session sign-off procedure | When symptom matches: "session already open", "can't log back in" |
| `case-5.9-invoice-rollback.md` | Invoice rollback data correction procedure | When symptom matches: "need to roll back invoice [N]" — ⚠️ Pattern C |
| `case-5.10-webshop-json.md` | Webshop JSON error data correction procedure | When symptom matches: "webshop is empty", "lots disappeared from webshop" — ⚠️ Pattern C |
| `CONTRIBUTING.md` | Contribution conventions, what is stable vs volatile, PENDING flag rules | Before making any changes to the repo |

---

## Conventions

**Screenshot convention:** Screenshots live in `assets/case-5.X/` and are numbered in step order. Reference them as `![Description](assets/case-5.X/0N-description.png)`. They can be updated independently of the case file text.

**PENDING flag convention:** Any unconfirmed detail is marked with a `> ⚠️ **PENDING**` block. Claude surfaces this flag to the agent during a live call. Do not remove a PENDING flag by guessing — confirm with Martijn first.

**Case file format:** All 10 case files follow the same template. Do not add sections or reorder sections.

**File-level marker:** Every file begins with `>>// ` and ends with `\\<<`.

\\<<
