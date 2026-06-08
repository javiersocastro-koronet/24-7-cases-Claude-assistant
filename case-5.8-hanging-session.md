>>// 

# Case 5.8 — Hanging Session
**Pattern:** B — Verify and Route | **Guidebook section:** 5.8

---

## Trigger phrases — what the customer says

| Phrase | Time of call | Probability |
|---|---|---|
| "session already open" / "ABS says my session is already active" | Any | Very high |
| "can't log back in" / "ABS won't open" / "disconnected and now can't get in" | Any | Very high |
| "I closed the window but the session is still there" | Any | High |

---

## Step-by-step resolution

This is a **diagnose and hand off** case, but the resolution (signing off the disconnected session) is performed by the 24/7 agent during the call.

**Step 1 — Get the customer's username and RDS server**
Ask the agent for:
- The **exact username** of the user who is locked out
- The **customer name** (to look up the RDS server in Confluence)

**Step 2 — Identify the RDS server**
Query Confluence for the customer: https://vertical.atlassian.net/wiki/spaces/A/pages/6321869774/ABS+Services+locations+customers
Look up the **RDS/RA server** column (also listed as Remote App in some rows).

> ⚠️ Some customers have **multiple RDS servers**. If you are not sure which one the user was connected to, you may need to check all of them.

**Step 3 — RDP to the RDS server as administrator**
RDP to the RDS server identified in Step 2 using an administrator account.

**Step 4 — Open Task Manager → Users tab**
Open **Task Manager** (Ctrl+Shift+Esc or right-click taskbar).
Navigate to the **Users** tab.
Find the user whose session is stuck. Their status will show as **Disconnected**.

**Step 5 — Sign Off the disconnected session**
Right-click the user's disconnected session → **Sign Off**.

> ⚠️ **Use Sign Off, NOT Disconnect.** Disconnect leaves the session open in a disconnected state — which is exactly the problem you are solving. Sign Off terminates the session cleanly.

**Step 6 — Confirm with the customer**
Tell the agent: the session has been signed off. Ask the customer to attempt to log back in to ABS now.
Confirm the login succeeds and close the call.

**Step 7 — Escalate to Tier 2**
If the session cannot be found, or if signing off the session does not resolve the login issue:
Tell the agent: "Proceed to escalate to Tier 2."
Brief: customer name, username, RDS server checked, what was found in Task Manager, what was tried.

---

## Important nuances

> ⚠️ Always confirm with the agent that the user has **fully closed the ABS window** (not just minimized it) before signing off the session. Signing off an active session will disrupt the user's work.

> ⚠️ The root cause of this issue is how Remote Desktop handles window close events: closing the ABS Remote App window ends the visible window but leaves the underlying RDP session as "Disconnected." This is expected behavior, not a bug — the customer just needs the session terminated.

> ⚠️ This procedure is performed on an **RDS server**, not on an AST or COM server. Do not look for the session on ABS application servers.

---

## Quick summary

If a customer says "session already open" / "can't log back in":
1. Get: username + customer name
2. Confluence → RDS/RA server column → identify RDS server (may be multiple)
3. RDP to RDS server as admin
4. Task Manager → Users tab → find Disconnected session
5. Right-click → **Sign Off** (NOT Disconnect)
6. Confirm customer can log back in
7. If unresolved at 20 min → escalate to Tier 2

\\<<
