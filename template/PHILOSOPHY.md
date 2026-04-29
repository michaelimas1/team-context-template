# Team Context Philosophy

Guidelines for contributing to and maintaining this repo. For the full pitch and background, see the [project README](https://github.com/michaelimas1/team-context-template).

## Principles

### 1. Pointers, Not Copies

Never copy system code, live data, or API responses into this repo. Only provide pointers - repo paths, dashboard URLs, API endpoints, Slack channel IDs. This way:
- The AI always fetches the real-time source of truth
- The repo doesn't go stale when code changes
- There's no drift between "what the docs say" and "what the code does"

### 2. Let the AI Interview You

Don't sit down and try to write a skill document from scratch. That's the old way. Instead:
- Tell Claude you want to teach it a new process
- Let it ask you questions to extract the information
- Review what it produces and correct it
- The result is better than what you'd write manually because it's structured for AI consumption

### 3. Force the AI to Update Itself

Make it a team habit: at the end of every successful novel workflow, tell Claude to capture what it learned. It opens a PR. This is non-negotiable - if the learning stays in one person's head (or worse, in memory), the team doesn't benefit.

### 4. Treat the AI as a UI

Every time you find yourself clicking through a web interface - monday.com boards, Salesforce, dashboards, admin panels, Slack - ask: "Can I turn this into a skill?" If the answer is yes, do it. The skill becomes a faster, more consistent interface than the GUI.

### 5. Keep It Clean

A messy team context is worse than none - it causes hallucinations and wrong answers with high confidence. Maintenance rules:

- **RALPH lifecycle:** Review, Audit, Learn, Prune, Handoff (see `docs/QUICKSTART.md` for details)
- **Schema compliance:** System docs follow templates (see `systems/README.md`). Don't let stubs accumulate.
- **Run `/health-check` periodically** to find stale docs, unfilled TODOs, and template drift
- **Kill dead skills** rather than leaving them to pollute routing

## Anti-Patterns

| Don't | Why | Do Instead |
|-------|-----|------------|
| Write massive docs upfront | No immediate ROI, goes stale fast | Build incrementally through the flywheel |
| Copy code or data into the repo | Drifts from source, creates false confidence | Use pointers - repo paths, URLs, IDs |
| Save system knowledge to memory | Only one person benefits, can't be reviewed | Commit to the repo via PR |
| Let skills rot | Stale skills cause wrong actions with high confidence | Run `/health-check`, prune quarterly |
| Skip the retro | Breaks the flywheel, knowledge stays tribal | Always capture learnings after novel workflows |
| Overload CLAUDE.md | Wastes context window on every task | Put details in subsystem files |

## How Agents Navigate This Repo Efficiently

With a repo full of system docs, references, and skills, how does the AI avoid loading everything at once and overwhelming its context window?

The answer is **progressive disclosure** - give the AI a map, not a library.

```
1. CLAUDE.md (always loaded)
   - Tells Claude what this repo is and how it's structured
   - Points to where specific knowledge lives

2. Targeted files (loaded on demand)
   - "Check the analytics dashboard" -> Claude reads systems/owned/analytics.md
   - "Create a task" -> skill triggers, loads its SKILL.md

3. Deep knowledge (loaded within skills)
   - SKILL.md says "for this system, read knowledge/system-specific.md"
   - Only loaded when the specific context is needed
```

The result: Claude gets exactly the context it needs for each task, nothing more.

## What Belongs Where

| Content type | Location | Example |
|-------------|----------|---------|
| How the repo works, team identity | `CLAUDE.md` | Team name, Slack channel, board IDs |
| System architecture, entry points | `systems/owned/*.md` | Service architecture, dashboard structure |
| Systems we depend on | `systems/reference/*.md` | Shared platforms, upstream dependencies |
| Stable lookup data | `references/*.md` | Slack channel IDs, monday.com column keys |
| Repeatable workflows | `.claude/skills/*/SKILL.md` | Sprint review, board processing |
| Heavy reference within a skill | `.claude/skills/*/knowledge/*.md` | System-specific queries or configs |
| Code-level conventions | That code's repo `CLAUDE.md` | TypeScript patterns, SQL conventions |
| User preferences | Memory | "I prefer ASCII diagrams" |
