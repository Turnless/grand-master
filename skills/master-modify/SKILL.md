---
name: master-modify
description: Master 2 (Modify). Changes, adds, or removes a coding pattern when the user changes their mind (e.g. "from now on use hooks for fetching"). Writes the proposed change to .claude/patterns/draft.md for review. Use when the user runs /master-modify.
disable-model-invocation: true
---

# Master 2: Modify

Your job is to **change the rules** when the user decides to work differently. You write proposals only. You never edit `grand-master/SKILL.md` (that's `/master-review`'s job), and you don't rewrite code unless the user explicitly asks.

Usage examples:
- `/master-modify from now on all errors should show a toast`
- `/master-modify rename components to kebab-case`
- `/master-modify remove FETCH-2`
- `/master-modify` with no request: ask "Which rule do you want to change, add, or remove?" and show the current rule list (ID + one line each).

---

## Step 1: Understand the request
Read `.claude/skills/grand-master/SKILL.md` and `.claude/patterns/draft.md`. Work out which of these it is:
- **change** an existing rule (find its ID)
- **add** a brand-new rule
- **remove** a rule

If the request is vague, ask one clarifying question.

## Step 2: Explain the change in plain language
Show the user:

> **Current rule (FETCH-1):** fetch data in a hook.
> **New rule:** fetch data in a hook *and* show a toast on error.
> **What this means for you:** every new data hook will also tell the user when something fails. Old hooks will still work, but they won't match the new rule until updated.

If the new rule **clashes with another rule**, say which one and ask how to resolve it.
If the change is likely a bad idea (e.g. it would make code harder to maintain), say so honestly in one or two sentences and suggest an alternative. The user still decides.

## Step 3: Find the affected code
Search the repo for files that follow the **old** way. List them (path + one-line note). This is the "drift list": code that won't match the new rule.

## Step 4: Write the proposal to the draft
Append to `.claude/patterns/draft.md`:

```markdown
### FETCH-1: Fetch data in a hook and show a toast on error
- **Status:** user-approved
- **Type:** modify  (or: new / remove)
- **Replaces:** FETCH-1 (old text: "Fetch data in a hook")
- **Reason for change:** user's words, short
- **Golden file:** path, or "none yet, will exist after first update"
- **Do:** ...
- **Don't:** ...
- **Why:** ...
- **Example:**
  (10–20 lines)
- **Drift list:** `src/a.ts`, `src/b.ts` (3 files follow the old rule)
```

For **remove**, only include Status, Type, Replaces, and Reason.

## Step 5: Offer to update old code (optional)
Ask: *"Do you want me to update the N files in the drift list now, or leave them for later?"*
- If yes: update them in small batches, show what changed, and pick the best updated file as the new golden file.
- If no: leave them. `/master-review` will keep reporting them.

## Step 6: Report back
Summarize the change in two or three lines and end with:
**Next step:** "Run `/master-review` to make this official."
