>>// 

# Case 5.5 — ALPS Labels
**Pattern:** A — Service Restart | **Guidebook section:** 5.5

---

## Trigger phrases — what the customer says

| Phrase | Time of call | Probability |
|---|---|---|
| "labels stopped" / "warehouse stuck" / "no labels coming out" | Any | Very high |
| "print jobs not processing" / "ALPS not running" | Any | Very high |
| "labels queued but not printing" | Any | High |

---

## Step-by-step resolution

Follow in order. If the fix works at any step, confirm with the customer and close.

**Step 1 — Identify the ALPS server**
Query Confluence for the customer: https://vertical.atlassian.net/wiki/spaces/A/pages/6321869774/ABS+Services+locations+customers
Look up the **ALPS (Axerrio Printjob Processor)** column. For large customers, ALPS may be on a dedicated server separate from the AST server.

**Step 2 — Check the ALPS database for job status**
Connect to SQL Server Management Studio (SSMS) on the ALPS server.
Run the following query to check recent print jobs:

```sql
SELECT TOP 100 * FROM <customer>_alps.dbo.labelprintjob ORDER BY [key] DESC
```

Replace `<customer>` with the customer code (e.g., `B2K_alps`, `EZF_alps`).

Status values:
- **0 = Inserted** — job queued, not yet processed
- **1 = Processed** — job processed by ALPS, sent to printer
- **2 = Error** — processing failed

> ⚠️ **PENDING — confirm before using in a live call:**
> Confirm the exact ALPS database naming convention for each customer. The pattern `<customer>_alps` is derived from the guidebook but may vary. Verify against SSMS on the relevant server.
> *Confirm with: Martijn | Added: 2026-06-08*

**Step 3 — Interpret job status**
- Jobs stuck at status **0 (Inserted)** → ALPS service is stopped → proceed to Step 4
- Jobs at status **2 (Error)** with error code **1801** → printer unreachable → proceed to Case 5.7 (Connection Problems)
- Jobs at status **1 (Processed)** but no physical output → ALPS processed the job, but the print spooler or printer is the issue → proceed to Step 5

**Step 4 — Restart the ALPS service (for status 0)**
RDP to the ALPS server. Open **Windows Services** (services.msc).
Locate the service named **"[Customer] Axerrio Print Job Processor"** (name includes customer code).
Stop the service. Wait **8–10 seconds**. Start the service.

![Stop, wait 8–10 seconds, Start](assets/common/start-stop-service.png)
Re-run the status query — jobs should begin moving from status 0 to status 1.

**Step 5 — If jobs are status 1 but no physical output: restart Print Spooler**
If ALPS has processed the jobs (status 1) but nothing is printing:
RDP to the ALPS server. Open **Windows Services**.
Locate **Print Spooler**. Stop → wait 8–10s → Start.
Confirm with the customer that labels are now printing.

**Step 6 — Optional: test print via ABS**
If needed, confirm the printer is reachable via ABS:
1. Press **F6** in ABS to open the print dialog
2. **Pause** the print queue temporarily
3. Send a test label
4. Check the print queue — confirm the document appears with the correct number
5. Resume the queue

**Step 7 — If Print Spooler restart did not resolve: check network connectivity**
If labels are still not printing after restarting Print Spooler, the printer may be unreachable.
Proceed to **Case 5.7** (Connection Problems) for network diagnostics.

**Step 8 — Escalate to Tier 2**
If unresolved, or at the 20-minute mark — stop and escalate.
Tell the agent: "Proceed to escalate to Tier 2."
Brief to give the specialist: customer name, ALPS job statuses found, error codes if any, what was restarted, result.

---

## Important nuances

> ⚠️ Error code **1801** in the ALPS error column means the printer is unreachable — this is a network issue, not a service issue. Do not repeatedly restart ALPS for error 1801. Route to Case 5.7 immediately.

> ⚠️ For large customers, ALPS runs on a **dedicated server**. Always check the ALPS column in Confluence separately from the ABS Windows Services column — they may point to different servers.

> ⚠️ The ZPL label format can be visualized using **Labelary** (labelary.com) if needed for debugging label content issues. This is for Tier 2 reference only.

---

## Pending items

> ⚠️ **PENDING — confirm before using in a live call:**
> Exact ALPS database naming convention per customer. Pattern `<customer>_alps` needs confirmation for each active customer.
> *Confirm with: Martijn | Added: 2026-06-08*

---

## Quick summary

If a customer says "labels stopped" / "warehouse stuck":
1. Confluence → ALPS column → identify server (may be separate from AST)
2. SSMS → `SELECT TOP 100 * FROM <customer>_alps.dbo.labelprintjob ORDER BY [key] DESC`
3. Status 0 → restart ALPS service (Stop → wait 8–10s → Start) → re-check
4. Status 2 + error 1801 → Case 5.7 (printer unreachable)
5. Status 1 but no output → restart Print Spooler → confirm
6. Still failing → Case 5.7 for network diagnostics
7. If unresolved at 20 min → escalate to Tier 2

\\<<
