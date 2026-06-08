>>// 

# Case 5.7 — Connection Problems
**Pattern:** B — Verify and Route | **Guidebook section:** 5.7

---

## Trigger phrases — what the customer says

| Phrase | Time of call | Probability |
|---|---|---|
| "labels processed but nothing prints" / "ALPS processed the job but nothing comes out" | Any | Very high |
| "connection error" / "printer unreachable" | Any | Very high |
| "Case 5.5 resolved (ALPS/Print Spooler restarted) but still no output" | Any | Very high |

---

## Step-by-step resolution

This is a **diagnose and hand off** case. You run one diagnostic check, then route to the correct party. You do not fix the network issue yourself.

**Step 1 — Confirm you are arriving from Case 5.5**
This case is most commonly triggered from Case 5.5 when:
- ALPS jobs show status 1 (Processed) but no physical output, AND
- Print Spooler has already been restarted with no improvement

If arriving directly from a customer call: confirm that ALPS is running and jobs are processing (status 1) before running this diagnostic. If ALPS itself is not running, resolve Case 5.5 first.

**Step 2 — Get the printer's IP address and port**
On the ALPS server:
Open **Print Management** → find the relevant printer → right-click → **Properties** → **Configure Port**
Note the **IP address** and **port number** for the printer.

**Step 3 — Run the network connectivity test from the ALPS server**
Open **PowerShell ISE** as Administrator on the **ALPS server** (not on your own laptop — the test must run from the server that sends the print jobs).

Run:
```powershell
Test-NetConnection <printer-ip> -port <printer-port>
```

Replace `<printer-ip>` and `<printer-port>` with the values from Step 2.

**Step 4 — Interpret the result and route**

| Result | Meaning | Action |
|---|---|---|
| `TcpTestSucceeded : True` | Network connectivity is fine — the problem is elsewhere | Escalate to Tier 2 |
| `TcpTestSucceeded : False` | Printer is unreachable from the server | Route to 24x7.nl or customer's ICT partner |

**Step 5a — If TcpTestSucceeded = True: escalate to Tier 2**
Tell the agent: "Proceed to escalate to Tier 2."
Brief: customer name, printer IP and port, `TcpTestSucceeded = True`, ALPS job status 1 confirmed, Print Spooler restarted.

**Step 5b — If TcpTestSucceeded = False: route to network party**
Tell the agent: "The printer is unreachable from the ALPS server. This is a network issue. Route to [24x7.nl / customer's ICT partner — confirm which applies for this customer]."
Brief: customer name, ALPS server name, printer IP and port, `TcpTestSucceeded = False`.

> ⚠️ **PENDING — confirm before using in a live call:**
> For each customer, confirm whether network issues should be routed to **24x7.nl** or to the **customer's own ICT partner**. This varies by customer. Check with Martijn or confirm via the customer's service agreement.
> *Confirm with: Martijn | Added: 2026-06-08*

---

## Important nuances

> ⚠️ The `Test-NetConnection` command **must be run from the ALPS server**, not from the agent's laptop. Network paths between servers and printers are different from network paths between laptops and printers.

> ⚠️ This case does **not** result in a fix by the 24/7 agent. The outcome is always either Tier 2 escalation or routing to a network team. Do not attempt further steps.

---

## Quick summary

If ALPS processed jobs (status 1) but nothing prints after Print Spooler restart:
1. Print Management → printer → Configure Port → note IP + port
2. PowerShell ISE as admin **on ALPS server** → `Test-NetConnection <ip> -port <port>`
3. True → escalate to Tier 2
4. False → route to 24x7.nl or customer's ICT partner (PENDING: confirm per customer)
5. If unresolved at 20 min → escalate to Tier 2

\\<<
