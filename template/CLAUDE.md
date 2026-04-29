# {{TEAM_NAME}} - Team Context

## Purpose

This repo is the team's **Team Context** - a centralized, version-controlled knowledge base designed for AI-first consumption. It transforms Claude from an isolated tool into a team member that understands our systems, workflows, conventions, and tribal knowledge.

Everything here is structured for **progressive disclosure**: Claude loads only what's needed for the current task, not the entire knowledge base. CLAUDE.md (this file) is always loaded. Everything else is loaded on demand.

For the philosophy behind this approach - why we built it this way, the flywheel effect, and the principles that keep it healthy - see `PHILOSOPHY.md`.

## How This Repo Works

```
CLAUDE.md (always loaded)
  |
  +-- references/    (on demand - stable lookup data: IDs, contacts, boards)
  +-- systems/       (on demand - architecture maps of what we own)
  +-- .claude/skills/ (on trigger - loaded when a skill phrase matches)
       +-- knowledge/  (on demand within skills - heavy reference content)
```

**Rule:** Load files proactively when they're relevant to the task at hand - don't wait for the user to ask. But don't preload everything at session start.

## Reference Files

| File | Contains |
|------|----------|
| `references/team.md` | Team roster (single source of truth) - names, Slack IDs, GitHub logins, emails |
| `references/other_teams.md` | Other teams we work with - on-call aliases, contacts |
| `references/slack.md` | Channel IDs, cross-team channels, user groups |
| `references/monday_boards.md` | Monday board IDs, workspace IDs, column keys |

## Systems Reference

Load the relevant file when working on a system-specific task.

**Owned systems:**

| System | File |
|--------|------|
| _(populated during `/setup`)_ | |

**Reference systems (we depend on, don't own):**

| System | File |
|--------|------|
| _(populated during `/setup`)_ | |

## Knowledge Routing

When you learn something new during a task, route it to the right place:

| What you learned | Where it goes | How |
|-----------------|---------------|-----|
| System behavior, API quirk, failure mode | `systems/owned/<system>.md` | Edit the file, open PR |
| Skill improvement, missing step, wrong assumption | `.claude/skills/<name>/SKILL.md` or `knowledge/` | Edit the file, open PR |
| Team convention, workflow rule | This file (`CLAUDE.md`) or `references/` | Edit the file, open PR |
| User's personal preference or style | Memory | Save to memory |
| Ephemeral task state | Tasks/plans | Use task tools |

**Never save system or team knowledge to memory.** Memory is for individual user preferences only (e.g. "I prefer ASCII diagrams"). Anything about the team, its tech stack, how systems work, how workflows run, or how this repo operates belongs in the repo where the whole team benefits. If you're unsure, it goes in the repo.

After completing a novel workflow or discovering something non-obvious, suggest running `/retro` to capture the learning properly.

## Content Boundary

If the content would break when the code it describes changes, it belongs in that code's repo. If it's useful regardless of which repo you're in, it belongs here.

- **Pointers, not copies** - link to code repos, API specs, dashboards. Never duplicate live data or code.
- **Progressive disclosure** - system-specific details (API URLs, credentials, endpoints) belong in `systems/` or skill `knowledge/` files, not here.
- **Skills tied to a codebase** live in that repo, not here. This repo holds cross-cutting skills that span repos or don't touch code.

## Team

- **Group:** {{GROUP_NAME}}
- **Team:** {{TEAM_NAME}}
- **Tech stack:** {{TECH_STACK}}
- **Slack:** `#{{TEAM_SLACK_CHANNEL}}` ({{TEAM_SLACK_CHANNEL_ID}})
- **PagerDuty team:** `{{PD_TEAM_SLUG}}` (ID: `{{PD_TEAM_ID}}`, escalation policy: `{{PD_ESCALATION_POLICY_ID}}`)
- **Monday boards:** Iteration `{{MONDAY_TASKS_BOARD_ID}}`, Sprints `{{MONDAY_SPRINTS_BOARD_ID}}` (full details in `references/monday_boards.md`)
- **This repo serves the entire team.** Every skill and doc here is shared. Never evaluate content as if it benefits only one person.
- **Team type:** {{TEAM_TYPE}}
- **GitHub logins:** {{GITHUB_TEAM_LOGINS}}

## This Repo Has No Code

This repo contains only skills, system docs, and reference data. **No application code lives here.** When the user asks to build, fix, or debug something, the work happens in the relevant code repo:

| Domain | Code repo | How to start |
|--------|----------|--------------|
| _(populated during `/setup`)_ | | |

If the task is about this repo itself (skills, docs, references), work here directly.

## Task Routing

Different task types use different entry points. If a skill exists for it, use the skill - it has the full workflow.

| Task type | Start with |
|-----------|-----------|
| Task creation | `/pm-story` - writes the full story (Why, What, Done When) |
| Morning brief / standup | `/good-morning` |
| Capturing learnings | `/retro` |
| Checking repo health | `/health-check` |
| Knowledge extraction | `/curious-intern` - interviews you to fill knowledge gaps |
<!-- Add custom skills as you create them -->

## Morning Brief Sources

The `/good-morning` skill reads this table to determine what to include in the daily brief. Uncomment sources your team uses. The `/setup` wizard configures this automatically based on your team type.

| Source | Type | Config | Enabled |
|--------|------|--------|---------|
| Sprint tasks | monday | board: `{{MONDAY_TASKS_BOARD_ID}}`, sprints: `{{MONDAY_SPRINTS_BOARD_ID}}` | Yes |
| Unowned bugs | monday | board: `{{MONDAY_TASKS_BOARD_ID}}`, type: bug, unowned | Yes |
| Slack: team channel | slack | channel: `{{TEAM_SLACK_CHANNEL_ID}}` | Yes |
<!-- | Open PRs | github | org: YourOrg, repos: [list repos here] | No | -->
<!-- | Merged PRs | github | org: YourOrg, repos: [list repos here] | No | -->
<!-- | PagerDuty incidents | pagerduty | team: PD_TEAM_ID, escalation: PD_ESCALATION_POLICY_ID | No | -->

## Global Agent Directives

- **Be concise:** The team prefers brief, direct communication. Skip boilerplate explanations.
- **Load context proactively:** When a task mentions a system, load its system doc without being asked. But don't preload all files at session start.
- **Safety first:** Always ask for confirmation before executing mutating API calls, DB writes, Git pushes and Slack message sending. Unless triggered via a specific automation skill.
- **Slack tone:** Messages sent on behalf of the team should be energetic and use emojis - never bare/robotic. If the context is a thread, always reply in the thread (set `thread_ts`).
- **Monday task creation:** Always use the `/pm-story` skill when creating sprint tasks - it handles story writing (Why, What, Done When, Open Questions) on top of item creation. Don't go direct to the API.
- **Self-improvement:** When a workflow produces a non-obvious learning, suggest `/retro` to capture it in the repo. Don't let tribal knowledge stay in one person's memory.
- **Cross-team context:** If a task involves another team's systems, check `references/other_teams.md` for their context repo. You can read their system docs for integration context. Repo naming convention: `{team-slug}-context`.
