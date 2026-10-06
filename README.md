# Grand-Master 🧠

**Teach your AI to code like *you*, every day, in every session.**

Ask an AI to build a component today and it fetches data right inside it. Ask for the same thing tomorrow and it writes a custom hook with loading states and fancy error handling. Both work, but your codebase slowly turns into the work of ten different engineers who never met.

The AI isn't being careless. **Nothing in your repo tells it how *you* write code**, so it guesses, and it guesses differently each time.

Grand-Master fixes that. It learns your patterns, lets you approve them, and makes the AI follow them every session.

---

## Who it's for

| You are... | What you get |
|---|---|
| 🎨 **A vibe coder** *(the main audience)*: you build with AI and don't read much code | Your projects stop falling apart as they grow. The AI asks you simple questions ("A or B? I recommend B because...") instead of quietly making a mess. |
| 🌱 **A junior developer** | A clear record of your project's conventions, with the reasons behind them. It's a good way to learn *why* code is structured the way it is. |
| 🛠️ **A senior developer** | Your team's conventions written down, enforced on every AI-generated change, and audited for drift. Set "be terse" in `CLAUDE.md` and it skips the explanations. |

---

## How it works

Four skills: three **masters** that you run yourself, and one **grand-master** that the AI follows automatically.

```
 /master-learn ──┐
                 ├──► draft.md ──► /master-review ──► grand-master ──► loaded every session
 /master-modify ─┘                (the gatekeeper)
```

| Skill | Command | Job |
|---|---|---|
| **Master 1: Learn** | `/master-learn` | Scans your code, counts how you do things, and asks you to choose when your code does something two ways. |
| **Master 2: Modify** | `/master-modify <change>` | Changes, adds, or removes a rule when you change your mind, and lists the code still using the old way. |
| **Master 3: Review** | `/master-review` | Verifies proposed rules before they're added, then audits your code for anything that breaks them. |
| **Grand-Master** | *(automatic)* | Holds only verified rules. Loaded at the start of every session through `CLAUDE.md`. |

What it learns:
- 📁 where new features and files go
- 🌐 how you fetch data
- ⚠️ how you handle errors
- ⏳ loading and empty states
- 🏷️ how you name files, functions, and variables
- 🧩 components, state, styling, and types
- 🚫 things you never want in your code

Each rule points to a **golden file**: a real file from *your* repo that shows the pattern. AI copies a working example far more reliably than it follows a written description.

See a filled-in example: [`examples/grand-master-nextjs.md`](examples/grand-master-nextjs.md)

---

## Install

Requires [Claude Code](https://claude.com/claude-code).

### Option A: as a plugin (easiest)
In Claude Code, run:
```
/plugin marketplace add Turnless/grand-master
/plugin install grand-master@grand-master
```
Plugin commands are namespaced: `/grand-master:master-learn`, `/grand-master:master-modify`, `/grand-master:master-review`.

### Option B: copy the skills manually
Copy the three folders in `skills/` into your personal skills folder:

- **macOS / Linux:** `~/.claude/skills/`
- **Windows:** `C:\Users\<you>\.claude\skills\`

```bash
git clone https://github.com/Turnless/grand-master.git
cp -r grand-master/skills/* ~/.claude/skills/
```

Commands: `/master-learn`, `/master-modify`, `/master-review`.

> Nothing needs to be copied into your projects. The first `/master-learn` in a project creates the grand-master and `CLAUDE.md` for you. If you already have a `CLAUDE.md`, it only adds one line.

---

## Quick start

In any project:

1. **`/master-learn`.** Answer its questions. If you're not sure, pick the option it recommends.
2. **`/master-review`.** Check the table it shows you and say yes to promote the rules.
3. **Build as usual.** The AI now follows your rules and tells you which one it used (e.g. "followed FETCH-1").

### When to run what
| Situation | Run |
|---|---|
| First time in a project | `/master-learn`, then `/master-review` |
| You want something done differently **from now on** | `/master-modify <what to change>`, then `/master-review` |
| You want something different **just this once** | Ask normally, no master needed |
| After building a feature | `/master-review audit recent` |
| Weekly, or before a release | `/master-review` |

> 💡 **Use git.** Commit after each `/master-review` so you can always undo a rule change.

---

## Make it yours

This is meant to be reshaped. Everything is plain Markdown, so open the files and edit them.

- **Add a category** (testing, database, API routes): add a section to `skills/master-learn/templates/grand-master.md` and a row to the table in `skills/master-learn/SKILL.md`
- **Change how strict "consistent" is:** `master-learn` treats 80% as "this is your pattern". Raise or lower it.
- **Change the explanation level:** edit the "About me" section of your project's `CLAUDE.md`
- **Rename the commands:** rename the skill folders and the `name:` line inside each `SKILL.md`

---

## Project layout

```
grand-master/
├── .claude-plugin/          ← plugin + marketplace config
├── skills/
│   ├── master-learn/
│   │   ├── SKILL.md
│   │   └── templates/       ← grand-master + CLAUDE.md starters
│   ├── master-modify/SKILL.md
│   └── master-review/SKILL.md
└── examples/                ← filled-in grand-masters for reference
```

Inside your project, after the first run:
```
your-project/
├── CLAUDE.md                          ← imports the grand-master
└── .claude/
    ├── skills/grand-master/SKILL.md   ← your verified rules
    └── patterns/
        ├── draft.md                   ← rules waiting for review
        └── changelog.md               ← history of every rule change
```

---

## Contributing

Forks, ideas, and example grand-masters for other stacks are all welcome. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE) © Abdulsalam Hassan
