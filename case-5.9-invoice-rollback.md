>>// 

# Case 5.9 — Invoice Rollback
**Pattern:** C — Data Correction | **Guidebook section:** 5.9

---

## ⚠️ Pattern C warning — read before proceeding

1. Default to the ABS interface, not the database
2. Get verbal confirmation from the customer before any destructive action
3. If the agent has never run this procedure before, ask Tier 2 or Martijn to walk through it first

---

## Trigger phrases — what the customer says

| Phrase | Time of call | Probability |
|---|---|---|
| "need to roll back invoice [N]" / "invoice was generated wrong" | Any | Very high |
| "wrong invoice number was issued" / "invoice needs to be regenerated" | Any | Very high |

---

## Step-by-step resolution

Follow in order. This procedure makes permanent changes to financial data. Do not skip steps or proceed past a PENDING flag.

**Step 1 — Get the invoice number from the customer**
Ask the agent to confirm: the **exact invoice number** to roll back.
Write it down. Repeat it back to the customer to confirm.

**Step 2 — Tell the customer to STOP ALL INVOICING immediately**
Before touching any data: instruct the customer (via the agent) to **immediately stop all invoice generation** in ABS.
Get explicit verbal confirmation that invoicing has been paused. Document this in Intercom with a timestamp.

**Step 3 — Check the invoice in the database**

> ⚠️ **PENDING — confirm before using in a live call:**
> Confirm the exact invoice table name. The guidebook references `ExInvoice` but this needs verification on each customer's database.
> *Confirm with: Martijn | Added: 2026-06-08*

Run the following queries to identify the key and the current seed:

```sql
-- 1. Check which key and number are in the correct book year invoice number range
SELECT * FROM bookyearinvoicenumberrange ORDER BY [key] DESC
SELECT number, * FROM ExInvoice ORDER BY [Key] DESC
```

Note:
- The **key** of the invoice to roll back
- The **current seed value** — write this down, you will need it to restore

**Step 4 — Locate and run the putbackinvoice script**

> ⚠️ **PENDING — confirm before using in a live call:**
> Confirm the exact storage location of the `putbackinvoice` snippet and the exact stored procedure name. The guidebook references the script but does not specify its location.
> Example reference from guidebook: `--axspCustomerRemoveInvoice 20169996`
> *Confirm with: Martijn | Added: 2026-06-08*

The procedure removes the specified invoice. Example (do not run until the exact procedure name is confirmed):

```sql
-- 2. Remove the invoice (CONFIRM EXACT PROCEDURE NAME BEFORE USE)
-- axspCustomerRemoveInvoice <invoice_number>
```

**Step 5 — Set the seed so the next invoice number is the affected one**

```sql
-- 3. Set the seed so that next invoice number is the affected one
-- UPDATE bookyearinvoicenumberrange SET seed = <target_invoice_number - 1> WHERE [key] = <key>
-- Example: UPDATE bookyearinvoicenumberrange SET seed = 20169995 WHERE [key] = 25
```

Replace `<target_invoice_number - 1>` with the invoice number minus 1, and `<key>` with the key identified in Step 3.

**Step 6 — Customer regenerates only the affected invoice**
Tell the agent: instruct the customer to regenerate **only the affected invoice** in ABS. Do not generate any other invoices until Step 7 is complete.
Confirm with the customer that the regenerated invoice has the correct number and content.

**Step 7 — Restore the seed to the original value**

```sql
-- 4. Put back the seed so that the next invoice number is the last one
-- UPDATE bookyearinvoicenumberrange SET seed = <original_seed> WHERE [key] = <key>
-- Example: UPDATE bookyearinvoicenumberrange SET seed = 20169997 WHERE [key] = 25
```

Replace `<original_seed>` with the value you wrote down in Step 3.

**Step 8 — Tell the customer to resume invoicing**
Confirm the seed is restored. Tell the agent: the customer may now resume normal invoicing.

**Step 9 — Document everything in Intercom**
Log the full procedure in Intercom with timestamps:
- Invoice number rolled back
- Time invoicing was paused
- Time invoicing was resumed
- Who confirmed at each step
- Original seed value, seed values set during procedure, final restored seed

**Step 10 — Escalate to Tier 2**
If unresolved, or at the 20-minute mark — stop and escalate.
Tell the agent: "Proceed to escalate to Tier 2."
Brief: customer name, invoice number, current state of the seed, whether invoicing is currently paused, what has been run so far.

---

## Important nuances

> ⚠️ **Do not proceed without explicit verbal confirmation** from the customer at each destructive step (Steps 4, 5, 6). Document each confirmation in Intercom with a timestamp.

> ⚠️ **Write down the original seed value before any changes.** If the restore step is missed or done incorrectly, subsequent invoices will have duplicate or incorrect numbers — a serious accounting error.

> ⚠️ If invoicing is not paused and another invoice is generated while the seed is manipulated, the resulting invoice number sequence will be corrupted. There is no easy undo.

---

## Pending items

> ⚠️ **PENDING — confirm before using in a live call:**
> 1. Exact storage location of the `putbackinvoice` snippet
> 2. Exact stored procedure name (guidebook example: `axspCustomerRemoveInvoice`)
> 3. Exact invoice table name (guidebook example: `ExInvoice`)
> *Confirm with: Martijn | Added: 2026-06-08*

---

## Quick summary

If a customer says "need to roll back invoice [N]":
1. Get exact invoice number — confirm with customer
2. Tell customer: **STOP ALL INVOICING NOW** — get confirmation + log in Intercom
3. SSMS: `SELECT * FROM bookyearinvoicenumberrange ORDER BY [key] DESC` + `SELECT number, * FROM ExInvoice ORDER BY [Key] DESC` → note key + current seed (write it down)
4. Run putbackinvoice script (PENDING: confirm exact procedure name and location)
5. `UPDATE bookyearinvoicenumberrange SET seed = <target-1> WHERE [key] = <key>`
6. Customer regenerates ONLY that invoice → confirm correct
7. `UPDATE bookyearinvoicenumberrange SET seed = <original> WHERE [key] = <key>`
8. Tell customer: resume invoicing
9. Document everything in Intercom with timestamps
10. If unresolved at 20 min → escalate to Tier 2

\\<<
