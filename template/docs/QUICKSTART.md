# Quickstart Guide

## Prerequisites

Before you start, make sure you have:

- [ ] **Claude Code** installed and working
- [ ] A **Monday.com workspace** with iteration (tasks) and sprints boards
- [ ] A **Slack channel** for your team
- [ ] At least **one system** your team owns (a service, platform, tool, or workflow - see `systems/README.md` for examples by team type)
- [ ] A **champion** - someone willing to drive adoption for the first 2 weeks (usually the team lead)

## Quickstart (3 Steps)

### 1. Create a new repo on GitHub

Or use an existing empty one. We recommend naming it `{team-slug}-context` (e.g. `growth-sales-context`, `bi-analytics-context`).

### 2. Pull in the template

```bash
cd your-repo
npx degit michaelimas1/team-context-template/template . && git add -A && git commit -m "init: team context from template"
```

This copies all the template files into your repo without git history.

### 3. Open Claude Code and say: "set up my team context"

The `/setup` skill will ask what kind of team you are, then walk you through filling in all the placeholders interactively. It adapts to your team type - engineering teams get GitHub and PagerDuty questions, other teams get a streamlined flow focused on Monday boards and Slack.

That's it. After setup, try saying "good morning" for your first morning brief.

## First 30 Minutes

### After setup (5 min)

Say "what skills do you have?" to verify everything loaded. You should see: retro, health-check, team-intro, list-skills, curious-intern, good-morning, and pm-story.

### Write your first system doc (20 min)

Pick your most critical system. Tell Claude:

> "I want to teach you about [System Name]"

Let Claude interview you. It will ask questions about what the system does, how you work with it, what breaks, and what other systems depend on it. It produces a system doc following the template. Review and commit.

**Do not try to write system docs from scratch.** The interview approach (from PHILOSOPHY.md: "Let the AI Interview You") produces better results because it's structured for AI consumption, not human reading.

### Try the morning brief (5 min)

Say "good morning." Claude will fetch your sprint tasks and Slack highlights (plus PRs and PagerDuty if your team has those configured). If board IDs or channel IDs are wrong, fix them in CLAUDE.md and references/.

## First Week

### Day 2-3: Add your systems

Write docs for 2-3 more owned systems. Use the interview approach every time. Each system doc should take 15-20 minutes.

Check the example docs in `systems/owned/` for what a well-written doc looks like - there are examples for engineering (`_example-active-system.md`), BI (`_example-analytics-system.md`), and sales (`_example-sales-system.md`) teams.

### Day 3-4: Create your first custom skill

Think of a workflow your team does repeatedly. Examples by team type:

**Engineering:** Debugging a service's failures, running a deployment checklist, investigating an alert
**BI / Analytics:** Refreshing a stale dashboard, investigating data quality issues, onboarding a new data source
**Sales:** Reviewing pipeline health, preparing for a QBR, auditing stale opportunities
**Marketing:** Launching a campaign, reviewing content performance, auditing automation rules
**Any team:** Reviewing incoming requests on a Monday board, preparing a weekly digest, onboarding a new team member

Tell Claude: "I want to create a skill for [workflow]." Use the `/skill-creator` plugin for a guided walkthrough.

### Day 5: Team demo

Show your team the morning brief and one custom skill. Let them try it. This is the **adoption ceremony** - seeing it work is what converts skeptics.

## First Month

### Week 2: Expand skills

Build 2-3 more skills based on real workflows. Every time you guide Claude through something new, end with `/retro` to capture the learning.

### Week 3: Reference data completeness

Fill in:

- All Monday board column keys (`references/monday_boards.md`)
- Cross-team contacts (`references/other_teams.md`)
- Additional Slack channels (`references/slack.md`)
- Any remaining team members (`references/team.md`)

### Week 4: Health check and retrospective

Run `/health-check` to see what's stale or incomplete. Run `/curious-intern` to extract tribal knowledge you haven't documented yet. Review what's working and what's not.

## Decision Trees

### "Where does this knowledge go?"

```
Is it about how a SYSTEM works (architecture, repos, failure modes)?
  -> systems/owned/<system>.md or systems/reference/<system>.md

Is it a REPEATABLE WORKFLOW (step-by-step process)?
  -> .claude/skills/<name>/SKILL.md

Is it STABLE LOOKUP DATA (IDs, contacts, channel names)?
  -> references/<topic>.md

Is it about HOW THIS REPO WORKS or TEAM CONVENTIONS?
  -> CLAUDE.md

Is it CODE-LEVEL CONVENTION tied to a specific codebase?
  -> That code repo's CLAUDE.md (not this repo)

Is it a PERSONAL PREFERENCE?
  -> Memory (the only thing that goes to memory)
```

### "Should this be a skill or a system doc section?"

```
Can Claude execute it end-to-end with MCP tools and bash?
  -> Skill

Is it reference information Claude needs to UNDERSTAND a system?
  -> System doc

Does it involve DECISIONS that need human judgment at each step?
  -> System doc (with a "When It Breaks" or "Runbook" section)

Is it a HYBRID (some automated steps, some human decisions)?
  -> Skill that guides the human through decision points
```

## Common Pitfalls

| Pitfall | Why it hurts | Prevention |
|---------|-------------|------------|
| Writing massive docs upfront | No immediate ROI, goes stale fast | Build through the flywheel - do real work, then `/retro` |
| Copying code into the repo | Drifts from source, creates false confidence | Use pointers: repo paths, URLs, IDs |
| Skipping `/retro` after novel work | Knowledge stays in one head | Make it a team habit - suggest it in standup |
| Too many placeholders/TODOs | Claude hallucinates or gives wrong answers | Fill stubs within a week or delete them |
| One person maintaining everything | Bus factor, burnout | Rotate "team context gardener" role weekly |
| Overloading CLAUDE.md | Wastes context window on every task | Put details in system docs and skills |
| Not running `/health-check` | Staleness accumulates silently | Run monthly, add to sprint retro agenda |
| Saving system knowledge to memory | Only one person benefits, can't be reviewed | Always commit to the repo via PR |

## The Adoption Ceremony

When introducing the team context to your team:

1. **Don't explain the philosophy first.** Show a demo instead.
2. **Live demo:** Open Claude Code, say "good morning", show the morning brief.
3. **Second demo:** Pick a real task from the sprint, run `/pm-story`.
4. **Third demo:** Show a system doc and how Claude uses it during a workflow.
5. **Then explain:** Share PHILOSOPHY.md. The flywheel concept lands better after seeing it work.
6. **Assignment:** Each team member writes one system doc or skill in the first week.

## Migration Guide: Existing Documentation

If your team has existing docs in Confluence, Notion, Google Docs, or wikis:

### What to migrate

- System architecture overviews -> `systems/owned/<system>.md`
- Runbooks and playbooks -> `.claude/skills/<workflow>/SKILL.md`
- Team roster and contact info -> `references/team.md`
- Board/channel/ID reference sheets -> `references/`

### What NOT to migrate

- Meeting notes (ephemeral, not useful for AI)
- Design docs (belong in the code repo)
- Onboarding guides written for humans (different audience than AI)
- Anything that duplicates what the code already says

### How to migrate

Don't copy-paste from Confluence. Instead:

1. Open the old doc and your new system doc template side by side
2. Tell Claude: "I'm migrating our [System] documentation. Here's what we have: [paste key points]"
3. Let Claude interview you to fill gaps
4. The result will be structured for AI consumption, not a copy of human docs

## Creating Your First Custom Skill

### When to create a skill

If you've guided Claude through the same workflow more than twice, it's time to make a skill. Signs you need one:

- You keep pasting the same instructions
- You keep reminding Claude about the same board IDs or API endpoints
- There's a checklist you follow every time

### How to create one

1. Tell Claude: "I want to create a skill for [workflow]" or use `/skill-creator`
2. Claude will help you structure the SKILL.md with:
   - YAML frontmatter (name, description with trigger phrases)
   - Step-by-step instructions
   - MCP tool calls with the right parameters
   - Error handling guidance
3. Save to `.claude/skills/<name>/SKILL.md`
4. Add it to the skill table in README.md
5. Test it by triggering it with one of your trigger phrases

### Skill format

```markdown
---
name: skill-name
description: When to trigger and what it does. Include trigger phrases.
---

# Skill Title

Step-by-step instructions for Claude to follow.
```

### Tips

- Keep SKILL.md under 500 lines. Use `knowledge/` files for heavy reference content.
- Write in imperative voice: "Read...", "Run...", "Ask the user..."
- Explain the *why* behind instructions, not just the *what*
- Include error handling: "If X fails, try Y"
- Test with 3-5 trigger phrases to verify routing accuracy

## Maintenance

### The RALPH Loop

Review your team context health quarterly:

- **R - Review:** Does each skill still match reality?
- **A - Audit:** Test trigger accuracy. Do the right skills fire?
- **L - Learn:** After bugs or incidents, update the relevant docs.
- **P - Prune:** Remove dead skills. A stale skill is worse than no skill.
- **H - Handoff:** If someone leaves, transfer their knowledge into the repo.

### Staying Current

- Run `/health-check` monthly
- Run `/curious-intern` after major system changes
- Update `last-reviewed` dates when you verify a system doc
- Delete example system docs (`_example-*`) once you have real ones
