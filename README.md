# Team Context

> A Git repo that teaches Claude about your team, so every session starts with a teammate instead of a stranger.

[**Quickstart**](#quickstart) · [**Features**](#what-your-team-gets) · [**Tips**](#tips) · [**FAQ**](#faq) · [**Adoption Guide**](template/docs/QUICKSTART.md)

## Why AI assistants fail at real work

AI models are already capable enough to run your sprint review, debug your alerts, prepare your QBR, or onboard a new hire. But most teams still use them for simple, isolated tasks. Why?

> **Because the bottleneck was never intelligence. It's context.**

Every AI session starts as a blank slate. Try asking Claude to do something real for your team, and watch it stumble on the basics:

- ❓ What's the business objective of the project your team is working on?
- ❓ Which monday board tracks your roadmap?
- ❓ Who owns the analytics pipeline?
- ❓ Which Slack channel handles escalations?

These aren't hard questions. Any teammate could answer them in seconds. But Claude can't - so before you can ask it to do anything meaningful, **you have to teach it your entire world. Every. Single. Session.**

This repo takes a different approach: instead of documentation written *for humans* that AI happens to read, it's a knowledge base designed *for AI consumption* from the start. And it updates itself through use.

---

## The Flywheel

The obvious way to give Claude context is documentation. But we all know what happens to docs - they go stale in months, nobody maintains them, and they become a source of fiction.

Team Context solves this with one key insight: **the documentation updates itself through use.**

```
    🆕  New task (never done before)
    │
    ▼
    🧑‍💻  You guide Claude through it,
    │   explaining where things live, correcting mistakes
    │
    ▼
    ✅  Task completed successfully
    │
    ▼
    📝  Claude captures what it learned,
    │   updates the relevant doc or skill, opens a PR
    │
    ▼
    ⚡  Next time anyone does this task,
    │   Claude executes it in seconds
    │
    └────► 🔁
```

The repo doesn't need a maintenance hero because doing real work IS the maintenance. Over time, effort drops and capability compounds.

> 💡 **The key habit:** After any novel workflow, tell Claude to capture what you learned. It commits the knowledge to the repo so the whole team benefits. Skip this step and the flywheel stops.

---

## What your team gets

Out of the box, your team context comes with these skills:

| Skill | What it does |
|-------|-------------|
| 👋 `/team-intro` | Team and project overview from the repo's own data - great for onboarding. |
| ☀️ `/good-morning` | Daily standup - sprint tasks, Slack highlights, what needs attention. Just say "good morning." |
| 📋 `/pm-story` | Structured monday task with full story - Why, What, Done When. |
| 🎙️ `/curious-intern` | Claude interviews you to extract tribal knowledge, then commits it to the repo. |
| 🔄 `/retro` | Captures learnings from any workflow and commits them back. This is the flywheel in action. |
| 🩺 `/health-check` | Finds stale docs and knowledge gaps before they cause problems. |

Teams typically build 5-15 custom skills on top of these within the first month.

---

## Quickstart

```bash
cd your-repo
npx degit michaelimas1/team-context-template/template .
git add -A && git commit -m "init: team context from template"
claude
```

Then say "set up my team context." The wizard fills in your team name, Slack channel, monday.com boards, and other details. Takes about 5 minutes.

When it's done, say "good morning" for your first daily brief.

> **Requirements:** [Claude Code](https://claude.ai/code), [Node.js](https://nodejs.org) (for `npx`)

We recommend naming your repo `{team-slug}-context` (e.g. `growth-context`, `platform-infra-context`). See [`docs/QUICKSTART.md`](template/docs/QUICKSTART.md) for the full adoption playbook.

---

## Principles

The flywheel only works if the repo stays trustworthy. These principles prevent it from becoming another stale wiki:

| | Principle | Why it matters |
|---|----------|--------------|
| 🔗 | **Pointers, not copies** | Link to dashboards, repos, and board IDs - never duplicate live data. The AI fetches the source of truth, so the repo can't go stale. |
| 🎙️ | **Let the AI interview you** | Don't write docs from scratch. Tell Claude what you want to teach it and let it ask the right questions. The result is structured for AI, not humans. |
| 🖥️ | **Treat the AI as a UI** | Every time you click through monday.com, Salesforce, or a dashboard - ask: "Can I turn this into a skill?" The skill is faster than the GUI. |
| 🧹 | **Keep it clean** | A messy team context causes confident wrong answers. Run `/health-check` periodically. Kill dead skills rather than letting them rot. |

---

## What's in the repo

```
CLAUDE.md              always loaded - the map of your knowledge base
references/            stable lookup data (IDs, contacts, board configs)
systems/               architecture docs for systems your team owns or depends on
.claude/skills/        automated workflows triggered by natural language
docs/QUICKSTART.md     adoption playbook
```

Claude loads only what's needed for the current task. Say "debug the analytics dashboard" and it reads that system doc. Say "good morning" and it checks your sprint board. Nothing is wasted.

---

## Works for every team

This isn't limited to engineering. Any team with monday.com boards and Slack can use a team context.

| Team type | Example "systems" you'd document |
|-----------|--------------------------------|
| Engineering | API services, CI/CD pipeline, data infrastructure |
| Data / BI | dbt project, Snowflake schemas, data pipelines |
| Sales | Salesforce pipeline, deal stages, quoting tool |
| Marketing | HubSpot campaigns, content CMS, audience segments |
| HR | SuccessFactors, recruiting pipeline, onboarding workflow |
| Finance | NetSuite, forecasting models, expense system |

The setup wizard adapts its questions based on your team type.

---

## Anti-patterns

| | Don't | Why | Do instead |
|---|-------|-----|------------|
| 📚 | Write massive docs upfront | No immediate ROI, goes stale fast | Build incrementally through the flywheel |
| 📎 | Copy code or data into the repo | Drifts from source, creates false confidence | Use pointers - repo paths, URLs, IDs |
| 🧠 | Save system knowledge to memory | Only one person benefits, can't be reviewed | Commit to the repo via PR |
| 🍂 | Let skills rot | Stale skills cause wrong actions with high confidence | Run `/health-check`, prune quarterly |
| ⏭️ | Skip the retro | Breaks the flywheel, knowledge stays tribal | Always capture learnings after novel workflows |
| 🏋️ | Overload CLAUDE.md | Wastes context window on every task | Put details in system docs and skills |

---

## Where does new knowledge go?

| Content type | Location | Example |
|-------------|----------|---------|
| How the repo works, team identity | `CLAUDE.md` | Team name, Slack channel, board IDs |
| System architecture, entry points | `systems/owned/*.md` | Service architecture, dashboard structure |
| Systems we depend on | `systems/reference/*.md` | Shared platforms, upstream dependencies |
| Stable lookup data | `references/*.md` | Slack channel IDs, monday.com column keys |
| Repeatable workflows | `.claude/skills/*/SKILL.md` | Sprint review, board processing |
| Code-level conventions | That code repo's `CLAUDE.md` | TypeScript patterns, SQL conventions |
| User preferences | Memory | "I prefer ASCII diagrams" |

---

## Tips

### Create a shell alias for instant access

Add a one-liner to your `~/.zshrc` (or `~/.bashrc`) so you can jump into your team context from anywhere:

```bash
# ~/.zshrc
alias yalla="cd ~/code/my-team-context && claude --dangerously-skip-permissions"
```

Pick a name that feels natural ("yalla", "tc", your team name) and point the path to your team context repo. Now you can open a terminal, type `yalla`, and you're in a fully contextual Claude session - no navigation, no setup.

> After editing your shell config, run `source ~/.zshrc` or open a new terminal for the alias to take effect.

---

## FAQ

### What if my team uses Cursor?

A team context repo is just structured files in a Git repo. Any AI tool that can read files can benefit from it.

Skills (`.claude/skills/`) are Claude Code-specific, but system docs (`systems/`), reference data (`references/`), and the knowledge structure in `CLAUDE.md` are tool-agnostic. Cursor, Windsurf, or any other AI tool can read these files and understand your team's systems, conventions, and workflows.

### Why do I need this if agents have auto memory?

Most AI tools now offer persistent memory - Claude Code has memory, Cursor has notes, others have similar features. These are useful for personal preferences ("I prefer ASCII diagrams", "always use TypeScript"), but they're siloed to one person. They can't be reviewed in a PR, audited for accuracy, or improved by the team.

A team context repo is the shared, version-controlled counterpart. Use memory for personal preferences. Use this repo for everything the team should know.

### What if OpenClaw or another tool replaces Claude?

The team context repo is a foundation layer, not a Claude bet. System docs, reference data, and organizational knowledge are plain markdown files - they work with any AI tool that can read files. If you switch tools tomorrow, the knowledge stays.

Skills are the only Claude Code-specific part, and they're just markdown instructions. Porting them to another tool's format is straightforward.

### How does Claude avoid loading everything at once?

With a repo full of system docs, references, and skills, the AI could overwhelm its context window by loading everything. Instead, the repo uses **progressive disclosure** - it gives the AI a map, not a library:

1. **`CLAUDE.md`** is always loaded. It tells Claude what this repo is and where specific knowledge lives.
2. **Targeted files** are loaded on demand. "Check the analytics dashboard" causes Claude to read `systems/owned/analytics.md`. "Create a task" triggers the pm-story skill.
3. **Deep knowledge** is loaded within skills. A skill's `SKILL.md` can reference additional `knowledge/` files, loaded only when that specific context is needed.

The result: Claude gets exactly the context it needs for each task, nothing more.

---

## Learn more

- [`docs/QUICKSTART.md`](template/docs/QUICKSTART.md) - full adoption playbook with decision trees, common pitfalls, and migration guide
