---
name: master-learn
description: Master 1 (Learn). Scans the codebase to discover how the user actually writes code (data fetching, error handling, naming, folder layout, and so on) and writes candidate patterns to .claude/patterns/draft.md. Use when the user runs /master-learn or asks to "learn my patterns".
disable-model-invocation: true
---

# Master 1: Learn

Your job is to **discover** patterns. You only observe and propose. You never edit code, and you never edit `grand-master/SKILL.md`.

Output goes to `.claude/patterns/draft.md`. Create the file if it doesn't exist.

The user may pass a focus area, e.g. `/master-learn error handling`. If they do, only study that area.

---

## Setup (first run in a project only)
Check the project root. Create anything that's missing, and tell the user what you created:
- `.claude/skills/grand-master/SKILL.md` missing → copy `templates/grand-master.md` from **this skill's folder**.
- `CLAUDE.md` missing → copy `templates/CLAUDE.md` from this skill's folder. Then ask the user one question: *"How much coding experience do you have, and how should I explain things?"* Write their answer into the "About me" section.
- `CLAUDE.md` exists but doesn't contain `@.claude/skills/grand-master/SKILL.md` → add that line near the top, under the first heading. Don't change anything else in the file.
- `.claude/patterns/draft.md` missing → create it with the heading `# Pattern Draft (waiting for /master-review)`.

## Step 0: Know what's already known
- Read `.claude/skills/grand-master/SKILL.md` (verified rules) and `.claude/patterns/draft.md` (pending).
- Skip anything already verified unless the code now clearly disagrees with it. In that case, add a `conflict` entry (see format).

## Step 1: Identify the stack
Read `package.json` (or `requirements.txt`, `pyproject.toml`, `go.mod`, etc.), config files, and `tsconfig.json`. Note:
- Language and framework (e.g. Next.js, React + Vite, Express)
- Data libraries (fetch, axios, React Query/TanStack, SWR, tRPC, ...)
- Validation, styling, state, and UI libraries

## Step 2: Map the folders
List the source tree 2–3 levels deep. Work out where pages/routes, components, hooks, API calls, types, and utils live, and **where a new feature would go**.

## Step 3: Study each category
For each category, search the code and **count** how many files use each approach. Look at real files, not just search hits.

| Category | What to look for |
|---|---|
| MAP | Where features, components, hooks, api, types, utils live |
| FETCH | `fetch(` / axios / useQuery / server actions; inside components vs hooks vs a service file |
| ERR | try/catch, `.catch`, error boundaries, toasts, thrown vs returned errors, error messages shown to users |
| LOAD | loading spinners/skeletons, empty-list messages, `isLoading` naming |
| NAME | file casing (`UserCard.tsx` vs `user-card.tsx`), hook names (`useX`), booleans (`isX`/`hasX`), handlers (`handleX`/`onX`), constants |
| COMP | function vs arrow components, default vs named exports, props typing, file size |
| STATE | useState, context, zustand/redux, URL state |
| STYLE | Tailwind, CSS modules, styled-components, class-name helpers |
| TYPE | `type` vs `interface`, where shared types live, validation (zod etc.) |

## Step 4: Classify what you found
- **Consistent** (about 80%+ of files do it one way) → status `candidate`
- **Split** (two or more real approaches) → status `needs-decision`
- **Too few examples** (fewer than 2 files) → don't record it yet; mention it in the summary

Pick a **golden file** for each candidate: the cleanest real example in the repo.

## Step 5: Ask the user about splits (plain language)
For every `needs-decision`, ask one question at a time. Use this format:

> **Data fetching: your code does this two ways.**
> - **A)** Fetch directly inside the component (4 files, e.g. `ProfilePage.tsx`)
> - **B)** Put fetching in a `useSomething` hook (3 files, e.g. `useOrders.ts`)
>
> **My recommendation: B.** The component stays simple, and the same fetch can be reused elsewhere.
> Which one should be your rule?

Match the explanation level in the "About me" section of `CLAUDE.md`. For beginners and vibe coders, avoid jargon, and explain any technical term in a few words. For experienced developers, be brief and technical. Record the answer and set the status to `user-approved`.

## Step 6: New or empty project?
If there is little or no code yet, don't invent patterns silently. Propose a **starter set** of sensible defaults for the detected stack (one short rule per category), explain each in a sentence, and ask the user to accept, change, or skip each one. Accepted ones become `user-approved`.

## Step 7: Write to the draft
Append entries to `.claude/patterns/draft.md` in this format:

```markdown
### FETCH-1: Fetch data inside a custom hook, never directly in a component
- **Status:** candidate | needs-decision | user-approved | conflict
- **Type:** new
- **Evidence:** 7 of 8 files. `src/features/orders/useOrders.ts`, `src/features/users/useUser.ts`
- **Exceptions seen:** `src/pages/Legacy.tsx` (fetches inline)
- **Golden file:** `src/features/orders/useOrders.ts`
- **Do:** ...
- **Don't:** ...
- **Why:** one plain sentence
- **Example:**
  (10–20 lines copied or trimmed from the golden file)
```

## Step 8: Report back
Give a short summary:
- X candidates found, Y decided by the user, Z areas without enough examples
- Any messy areas worth noticing
- **Next step:** "Run `/master-review` to verify these and add them to the grand-master."
