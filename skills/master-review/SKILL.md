---
name: master-review
description: Master 3 (Review). Verifies proposed patterns in .claude/patterns/draft.md and promotes the good ones into the grand-master, then audits the code for places that break the verified rules. The only skill allowed to edit grand-master. Use when the user runs /master-review.
disable-model-invocation: true
---

# Master 3: Review

You are the **gatekeeper**. Nothing reaches the grand-master without passing you. You have two jobs:

1. **Promote:** check the draft and move verified patterns into `.claude/skills/grand-master/SKILL.md`
2. **Audit:** check the code against the grand-master and report anything that breaks the rules

Arguments:
- `/master-review` runs both (promote first, then audit)
- `/master-review promote` runs only the promote job
- `/master-review audit` runs only the audit job. Use `audit recent` to check only files changed since the last commit (`git diff --name-only HEAD` plus untracked files).

---

## Job 1: Promote

Read `.claude/patterns/draft.md`. For **each** entry, run these checks:

| Check | Pass when |
|---|---|
| Decided | Status is `candidate` or `user-approved`. `needs-decision` and `conflict` entries **fail**: ask the user to decide, or tell them to run `/master-learn`. |
| Evidence is real | The golden file and the evidence files exist and actually do what the rule says. Open them and confirm. |
| Example is correct | The example uses libraries and imports that really exist in this project. No made-up helpers. |
| No contradiction | It doesn't clash with another rule in grand-master or the draft. |
| Clear | A different AI could follow it without guessing. If it's vague, rewrite it to be specific. |
| Small | Example is 20 lines or fewer. Trim if needed. |

Then:
- **Show the user a short table** (Rule ID, Pass or Fail, reason) and ask: *"Promote the passing ones?"* Wait for a yes.
- On yes, for each passing entry:
  - `new` → add it under the right section of grand-master, in the grand-master's rule format (drop Status/Evidence/Drift fields)
  - `modify` → replace the old rule with the same ID
  - `remove` → delete the rule
  - Remove the `_No verified rules yet._` line from any section that now has rules
- Update the grand-master header: set **Status** to `ACTIVE (N rules)` and **Last reviewed** to today's date.
- Append one line per change to `.claude/patterns/changelog.md` (create it if missing):
  `YYYY-MM-DD | added FETCH-1 | Fetch data inside a custom hook`
- Delete promoted entries from the draft. Leave failed ones there, with a `- **Review note:** ...` line saying why they failed.

**Keep grand-master short.** It loads every session, so a long file costs speed and focus. If it goes over ~250 lines, suggest merging similar rules or trimming examples.

## Job 2: Audit

Read grand-master. Check the code (all source files, or recent changes only if `audit recent`):
- For each rule, search for code that breaks it.
- Report as a list grouped by rule:
  > **ERR-1 (show a toast on errors)**: 3 files break it
  > - `src/features/cart/useCart.ts:24`: catches the error but shows nothing to the user

Then judge the pattern of breaks:
- **A few files break a rule** → probably drift. Offer: *"Want me to fix these to match the rule?"* Fix only if they say yes.
- **Most files break a rule** → the rule may be wrong, not the code. Say so and suggest `/master-modify`.
- **New patterns appear that no rule covers** → suggest `/master-learn <area>`.

## Report back
End with a plain-language summary:
- Rules promoted / failed
- Code issues found / fixed
- Health score: `X of Y rules fully followed`
- The one most useful next step
