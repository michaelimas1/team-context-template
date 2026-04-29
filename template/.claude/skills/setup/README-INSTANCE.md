# {{TEAM_NAME}} - Team Context

> {{TEAM_NAME}}'s knowledge base for AI. Structured for Claude, maintained by the team.

## Quick Start

Open Claude Code in this repo and say any of these:

| Say this | What happens |
|----------|-------------|
| "good morning" | Daily brief: sprint tasks, team updates, what needs attention |
| "create a task" | Structured Monday task (Why / What / Done When) |
| "interview me" | Claude extracts your knowledge and commits it to the repo |
| "let's retro" | Captures learnings after any workflow |
| "health check" | Finds stale docs and knowledge gaps |
| "what skills do you have?" | Lists all available skills |

## Skills

| Skill | Trigger phrases | Description |
|-------|----------------|-------------|
| `good-morning` | "good morning", "morning brief", "catch me up" | Daily brief with sprint tasks, team updates, and action items |
| `pm-story` | "create a task", "write a story", "document this task" | Structured task creation grounded in system context |
| `retro` | "let's retro", "capture what we learned" | Self-learning: captures knowledge, commits to repo |
| `health-check` | "health check", "stale docs" | Staleness detection for system docs and references |
| `curious-intern` | "interview me", "fill gaps", "brain dump" | Knowledge extraction interview to fill doc gaps |
| `team-intro` | "tell me about the team", "onboard me" | Team and project overview |
| `list-skills` | "what skills do you have", "list skills" | Show all available skills |
<!-- Add custom skills here as you create them -->

## Systems We Own

| System | Doc |
|--------|-----|
<!-- Systems are populated during setup and as you document more -->

See `systems/README.md` for doc templates. Say "teach me about [system name]" to write a new one.

## Team

**{{TEAM_NAME}}** - `#{{TEAM_SLACK_CHANNEL}}`

See `references/team.md` for the full roster.

## How This Repo Works

```
CLAUDE.md (always loaded - the map)
  |
  +-- references/    (stable lookup data: IDs, contacts, boards)
  +-- systems/       (architecture maps of what we own)
  +-- .claude/skills/ (automated workflows triggered by natural language)
```

Claude loads only what's needed for the current task. Details in `PHILOSOPHY.md`.

## Adding to This Repo

- **New skill**: say "I want to create a skill for [workflow]" or create `.claude/skills/{name}/SKILL.md`
- **New system doc**: say "teach me about [system name]" - Claude will interview you
- **Capture learnings**: say "let's retro" after any novel workflow
- **Check health**: say "health check" periodically to find gaps

## Adoption Guide

See `docs/QUICKSTART.md` for the full playbook: first 30 minutes, first week, first month, decision trees, and common pitfalls.

<details>
<summary>Git hooks setup</summary>

This repo includes a pre-commit hook for `gitleaks` (secret scanning):

```bash
git config core.hooksPath .githooks
```

The hook runs automatically if configured. If you don't have `gitleaks` installed, it will warn but not block commits.

</details>
