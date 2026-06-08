>>// 

# Contributing to This Repo

This repo is a live support tool. Mistakes here affect real support calls. Read this file before making any changes.

---

## Who maintains this

- **Content owner:** Martijn (procedure correctness, PENDING flag resolutions)
- **Repo structure:** Any team member familiar with the conventions below

---

## What is stable vs. volatile

**Stable** — change only when the system or procedure changes:
- `00-overview.md`
- All `case-5.X-*.md` files
- `assets/`

**Volatile** — grows regularly as new examples are added:
- `cases-index.md`

---

## Four conventions — read these before writing anything

### 1. Screenshot convention

Screenshots live in `assets/case-5.X/` and are numbered in the order they appear in the procedure steps. Reference them in case files as:

```
![Step N description](assets/case-5.X/0N-description.png)
```

Screenshots can be updated independently of the case file text.

### 2. PENDING flag convention

Any step that has an unconfirmed detail (e.g., exact script name, exact SQL syntax, exact tool URL) is marked with a PENDING block:

```
> ⚠️ **PENDING — confirm before using in a live call:**
> [specific detail that needs confirmation]
> *Confirm with: Martijn | Added: [date]*
```

Claude surfaces this flag to the CS agent when it reaches that step, and does not proceed past it as if the detail were known. **Do not resolve a PENDING flag by guessing.** Leave it marked until confirmed with Martijn.

### 3. Case file format

All 10 case files follow the same template. Do not add sections. Do not reorder sections. The template is defined in the design brief.

### 4. File-level marker

Every file begins with `>>// ` and ends with `\\<<`. This marks file boundaries when files are concatenated or when context is ambiguous.

---

## What NOT to do

- Do not write case content before understanding the template
- Do not mix procedure steps into `00-overview.md` — that file is routing logic only
- Do not add customer-specific data to the repo — that lives in Confluence
- Do not create a case file without at least one confirmed trigger phrase and at least one confirmed step
- Do not resolve a PENDING flag by guessing — leave it marked until confirmed

\\<<
