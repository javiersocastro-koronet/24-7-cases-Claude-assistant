>>// 

# Case 5.6 — Calculation Engines
**Pattern:** A — Service Restart | **Guidebook section:** 5.6

---

## Trigger phrases — what the customer says

| Phrase | Time of call | Probability |
|---|---|---|
| "missing lots on webshop" / "prices not calculating" | Any | Very high |
| "calculation engine stuck" / "calc queue backed up" | Any | Very high |
| "webshop prices wrong or missing" | Any | High |
| "calculation engine overflowed" (customer self-diagnosed) | Any | High |

---

## Step-by-step resolution

Follow in order. If the fix works at any step, confirm with the customer and close.

**Step 1 — Identify the server**
Query Confluence for the customer: https://vertical.atlassian.net/wiki/spaces/A/pages/6321869774/ABS+Services+locations+customers
Look up the **ABS Windows Services** column. Calculation Engine services run on the AST or COM server.

**Step 2 — Diagnose: run the stored procedure**
Connect to SSMS on the ABS database server for this customer.
Run:

```sql
exec axspCheckCalculationQueues
```

This returns:
- **Records to calculate** per engine — how many items are queued
- **Latest DQ timestamp** per engine — when the engine last processed something

**Interpretation:**
- High count + old timestamp + not moving when you re-run → engines are **hanging**
- Count dropping + recent timestamp → engines are running normally (issue may be elsewhere)

> ⚠️ **PENDING — confirm before using in a live call:**
> Confirm the stored procedure name `axspCheckCalculationQueues` is correct and present on all customer databases. The guidebook names this procedure but it should be verified on a live DB before first use.
> *Confirm with: Martijn | Added: 2026-06-08*

**Step 3 — Customer self-check (if customer has ABS access)**
Ask the customer to navigate in ABS: **Reports → Webshop → General → "Calculate Loss per Customer and Department"**
If the report shows **"calculation engine overflowed"** → confirmed, engines are hanging.

**Step 4 — Stop ALL calculation engines**
RDP to the server. Open **Windows Services** (services.msc).
Locate all services named **"Realtime Calc"** and **"Pricelist Calc"** (there may be multiple engines — stop ALL of them).

> ⚠️ **Critical:** Stop ALL engines before starting any. The engines depend on each other for queue ordering. Starting one before the others are stopped causes queue conflicts.

**Step 5 — Wait 10 seconds, then start ALL engines together**
Wait ~10 seconds after all engines are stopped.
Start ALL engines. Start them in sequence but without long delays between each — the goal is all engines running again.

**Step 6 — Verify**
Re-run:

```sql
exec axspCheckCalculationQueues
```

Confirm that the record counts are **dropping** and the DQ timestamps are **updating**. This confirms the engines are processing.

**Step 7 — Escalate to Tier 2**
If unresolved, or at the 20-minute mark — stop and escalate.
Tell the agent: "Proceed to escalate to Tier 2."
Brief to give the specialist: customer name, record counts and timestamps before and after restart, how many engines were found and restarted.

---

## Important nuances

> ⚠️ **Do not restart engines individually.** The Realtime Calc and Pricelist Calc engines coordinate queue ordering with each other. Restarting one while others are still running causes queue conflicts and may worsen the problem. Always stop ALL, wait, then start ALL.

> ⚠️ There may be **multiple instances** of each engine (e.g., Realtime Calc 1, Realtime Calc 2). Stop and restart all instances, not just the first one.

---

## Pending items

> ⚠️ **PENDING — confirm before using in a live call:**
> Confirm stored procedure name `axspCheckCalculationQueues` is valid on all customer databases.
> *Confirm with: Martijn | Added: 2026-06-08*

---

## Quick summary

If a customer says "prices not calculating" / "missing lots on webshop":
1. Confluence → ABS Windows Services column → identify server
2. SSMS → `exec axspCheckCalculationQueues` → check counts and timestamps
3. High count + old timestamp + not moving → engines hanging
4. services.msc → stop ALL Realtime Calc + Pricelist Calc engines
5. Wait 10 seconds → start ALL engines together
6. Re-run stored procedure → confirm counts dropping + timestamps updating
7. If unresolved at 20 min → escalate to Tier 2

\\<<
