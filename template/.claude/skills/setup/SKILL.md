---
name: setup
description: Use this skill when setting up a new team context for the first time, when the user says "set up my team context", "configure team context", "fill in placeholders", "initialize team context", "onboard my team", "set up", or when you detect that files still contain unfilled {{PLACEHOLDER}} values. This is the interactive onboarding wizard that replaces all template placeholders with real team data.
---

# Team Context Setup Wizard

This skill walks you through setting up your team's context. It replaces all the `{{PLACEHOLDER}}` values in the template with your real team data.

## Step 0: Check State

First, scan for unfilled placeholders:

```bash
grep -r '{{' --include='*.md' --include='*.json' . | grep -v node_modules | grep -v '.git/' | grep -v '_example' | head -30
```

If NO `{{` patterns found: tell the user setup is already complete. Suggest `/health-check` to verify everything looks good, or `/curious-intern` to start filling in knowledge gaps.

If placeholders are found: continue to Step 1.

---

## Step 1: Welcome

Say something like:

> Hey! Let's get your team context set up. I'll ask you some questions about your team, then fill everything in automatically. Takes about 5 minutes.
>
> I'll need some IDs from Slack and Monday - and depending on your team type, maybe GitHub and PagerDuty too. I'll tell you exactly where to find each one as we go.

---

## Step 1.5: Team Type

Ask:

> What kind of team are you? This helps me tailor the setup to your workflow.
>
> - **Engineering** (builds and ships software - product teams included)
> - **Data / BI / Analytics** (pipelines, dashboards, reports)
> - **Sales** (pipeline, deals, customer relationships)
> - **Marketing** (campaigns, content, launches)
> - **HR** (recruiting, onboarding, people ops)
> - **Finance** (budgets, forecasting, reporting)
> - **Other** (I'll tell you what we do)

Store the answer as `{{TEAM_TYPE}}`. This affects which questions you ask next and how you configure skills.

**Team type determines what to ask and skip:**

| Question area | Engineering | Data/BI | Sales | Marketing | HR | Finance |
|---------------|:-----------:|:-------:|:-----:|:---------:|:--:|:-------:|
| GitHub repos | Yes | Optional | Skip | Skip | Skip | Skip |
| PagerDuty | Yes | Optional | Skip | Skip | Skip | Skip |
| Monday boards | Yes | Yes | Yes | Yes | Yes | Yes |
| Slack channel | Yes | Yes | Yes | Yes | Yes | Yes |
| Systems owned | Yes | Yes | Yes | Yes | Yes | Yes |

---

## Step 2: Interview - Identity

Ask all of these in a single `AskUserQuestion` message:

Adapt the intro based on `{{TEAM_TYPE}}`:
- **Engineering teams:** "I'll need some IDs from Slack, Monday, GitHub, and PagerDuty."
- **Data/BI teams:** "I'll need some IDs from Slack and Monday - and optionally GitHub and PagerDuty if you use them."
- **All other teams:** "I'll need some IDs from Slack and Monday."

**Team basics:**
1. **Team name** - the display name (e.g. "Platform Infrastructure", "Growth Backend")
2. **Team slug** - for file names and URLs. I'll suggest one based on your team name - you can confirm or change it. (e.g. "platform-infra", "growth-backend")
3. **Group name** - the larger org group (e.g. "Platform", "Core Infrastructure", "Growth")
4. **Tech stack** - main languages and what they're used for (e.g. "TypeScript (services), Python (data pipelines)")

**Your details (first team member):**
5. **Name**
6. **Role** - team lead, IC, manager, etc.
7. **GitHub login**
8. **Slack ID** - to find this: right-click your name in Slack > "Copy member ID"
9. **Email**

**Manager** (skip if you are the manager):
10. **Name**
11. **Slack ID**
12. **Email**

**Main Slack channel:**
13. **Channel name** (without the #)
14. **Channel ID** - to find this: right-click the channel name > "View channel details" > scroll to bottom. Or "Copy link" and the ID is the last segment of the URL. Slack channel IDs start with `C`.

---

## Step 3: Interview - Systems & Tools

Ask all of these in a single `AskUserQuestion` message:

**Systems you own:**
1. List the systems/services your team owns and operates. For each one, give a name and one-line description. Minimum 1.
   Example: "Widget Service - handles widget CRUD and webhooks"

**PagerDuty (Engineering and Data/BI teams only - skip for other team types):**
2. **Team slug** - as it appears in PagerDuty
3. **Team ID** - to find this: PagerDuty > People > Teams > click your team > the ID is in the URL (starts with P)
4. **Escalation policy ID** - to find this: PagerDuty > People > Escalation Policies > click yours > the ID is in the URL (starts with P)
5. **PagerDuty subdomain** - the prefix in your PagerDuty URL. If your incidents live at `https://acme.pagerduty.com/...`, the subdomain is `acme`.

**Monday boards:**
6. **Tasks board ID** - to find this: open your iteration/tasks board > the number in the URL after `/boards/`. Board IDs are numeric (e.g. `18392881897`).
7. **Sprints board ID** - to find this: open your sprints board > the number in the URL. Also numeric.
8. **Workspace subdomain** - the prefix in your monday.com URL. If your boards live at `https://acme.monday.com/...`, the subdomain is `acme`.

**GitHub (Engineering and Data/BI teams only - skip for other team types):**
9. **Organization** - (e.g. "acme-corp")
10. **Repos** your team works in - org/name + brief description. At least 1.
    Example: "acme-corp/widget-service - main backend service"

**Team members** (optional - you can always add these later in `references/team.md`):
11. Additional team members: name, Slack ID, GitHub login, email

---

## Step 4: Generate the Slug

From the team name, suggest a slug:
- Lowercase
- Hyphens instead of spaces
- Short (e.g. "Platform Infrastructure" -> "platform-infra")

Confirm with the user before proceeding.

---

## Step 5: Apply Changes

Now replace all `{{PLACEHOLDER}}` values across every file.

### 5a. Build the replacement map

From the interview answers, build a map:

| Placeholder | Value |
|---|---|
| `{{TEAM_NAME}}` | [from interview] |
| `{{TEAM_SLUG}}` | [generated slug] |
| `{{TEAM_TYPE}}` | [from interview - engineering, data-bi, sales, marketing, hr, finance, other] |
| `{{GROUP_NAME}}` | [from interview] |
| `{{TECH_STACK}}` | [from interview] |
| `{{TEAM_SLACK_CHANNEL}}` | [from interview] |
| `{{TEAM_SLACK_CHANNEL_ID}}` | [from interview] |
| `{{PD_TEAM_SLUG}}` | [from interview] |
| `{{PD_TEAM_ID}}` | [from interview] |
| `{{PD_ESCALATION_POLICY_ID}}` | [from interview] |
| `{{PAGERDUTY_SUBDOMAIN}}` | [from interview - omit for non-eng/non-data teams] |
| `{{MONDAY_TASKS_BOARD_ID}}` | [from interview] |
| `{{MONDAY_SPRINTS_BOARD_ID}}` | [from interview] |
| `{{MONDAY_WORKSPACE}}` | [from interview] |
| `{{TEAM_LEAD_NAME}}` | [from interview] |
| `{{TEAM_LEAD_GITHUB}}` | [from interview] |
| `{{TEAM_LEAD_SLACK_ID}}` | [from interview] |
| `{{TEAM_LEAD_EMAIL}}` | [from interview] |
| `{{MANAGER_NAME}}` | [from interview] |
| `{{MANAGER_SLACK_ID}}` | [from interview] |
| `{{MANAGER_EMAIL}}` | [from interview] |
| `{{GITHUB_ORG}}` | [from interview] |
| `{{GITHUB_TEAM_LOGINS}}` | [pipe-separated GitHub logins] |
| `{{SETUP_DATE}}` | [today's date YYYY-MM-DD] |

### 5b. Replace placeholders in all files

For each file that contains `{{` patterns:
1. Read the file
2. Replace all `{{PLACEHOLDER}}` values with their real values from the map
3. Write the file back

Files to process:
- `CLAUDE.md`
- `README.md`
- `references/team.md`
- `references/slack.md`
- `references/monday_boards.md`
- `references/other_teams.md`
- `.claude/skills/good-morning/SKILL.md`
- `.claude/skills/pm-story/SKILL.md`

### 5b-extra. Enable pr-ship plugin for engineering/data-bi teams

For **engineering** and **data-bi** team types only, add the `pr-ship` plugin to `.claude/settings.json`:

```json
"pr-ship@agentic-builders-hub": true
```

Add this to the `enabledPlugins` object. Skip this for all other team types.

### 5c. Populate the Systems Reference table in CLAUDE.md

For each system the user listed, add a row to the owned systems table:

```markdown
| System Name | `systems/owned/system-slug.md` |
```

### 5d. Populate the Code Repo table in CLAUDE.md

For each GitHub repo the user listed, add a row:

```markdown
| Domain description | `Org/repo-name` | Load `systems/owned/relevant-system.md` |
```

### 5e. Add team members to references/team.md

If the user provided additional team members, add rows to the Team table.

### 5f. Build GitHub team login allowlist

Combine all GitHub logins (first member + any additional members) into a pipe-separated string (e.g. `user1|user2|user3`). Replace `{{GITHUB_TEAM_LOGINS}}` in CLAUDE.md (under the Team section's "GitHub logins" field).

### 5g. Create system doc stubs

For each owned system, create `systems/owned/<slug>.md` using the full template from `systems/README.md`:

```markdown
<!-- last-reviewed: YYYY-MM-DD -->
# System Name

> One-liner from the interview

## Overview
[To be filled in - tell Claude "teach me about [System Name]" or run /curious-intern]

## How Claude Works With This
| Action | How |
|--------|-----|
| Make changes | GitHub PRs to `Org/repo-name` |
| Investigate | [To be filled in] |
| Deploy | [To be filled in] |

## Repos
| Repo | What it contains |
|------|-----------------|
| `Org/repo-name` | [from interview] |

## Architecture
[To be filled in]

## Key Entry Points
- [To be filled in]

## Known Issues / Failure Modes
| Issue | Symptoms | Resolution |
|-------|----------|------------|
| [To be filled in] | | |

## Related Systems
- **Upstream:** [To be filled in]
- **Downstream:** [To be filled in]

## Pointers
- **Dashboards:** [To be filled in]
- **Alerts:** [To be filled in]
```

Set `last-reviewed` to today's date.

Adjust the system doc content based on team type:
- **Engineering teams:** Use "Repos" section, "Deploy" in How Claude Works With This
- **Data/BI teams:** Use "Data Sources" instead of "Repos", "Refresh" instead of "Deploy"
- **Sales/Marketing/HR/Finance:** Use "Platforms & Tools" instead of "Repos", omit "Deploy"

---

## Step 6: Show Summary

Before committing, show the user a summary:

```
Setup complete! Here's what I did:

- Filled X placeholders across Y files
- Created Z system doc stubs: [list them]
- Added N team members to the roster

Ready to commit these changes?
```

Use `AskUserQuestion` to confirm.

---

## Step 6.5: Replace README

Replace `README.md` with the post-setup version from `.claude/skills/setup/README-INSTANCE.md`:
1. Read `README-INSTANCE.md`
2. Replace all `{{PLACEHOLDER}}` values in it (same as other files)
3. Populate the Skills table with the 7 post-setup skills (not setup itself)
4. Populate the Systems table with any system stubs created
5. Write the result to `README.md`, replacing the pitch README

---

## Step 7: Commit

```bash
git add -A
git commit -m "init: set up team context for [team name]"
```

---

## Step 8: What's Next

```
Your team context is ready! Here's what to do next:

- [ ] Try it: say "good morning" for your first morning brief
- [ ] Say "teach me about [your most critical system]" to write your first real system doc
- [ ] Run /curious-intern when you have 15 min to fill knowledge gaps
- [ ] Fill in references/monday_boards.md with your board column keys
- [ ] Fill in references/other_teams.md with teams you work with
- [ ] Push to GitHub and share with your team
- [ ] Read docs/QUICKSTART.md for the full adoption playbook

Pro tip: After any new workflow, say /retro to capture what you learned.
```

---

## Step 9: Self-Destruct (Optional)

Ask: "Want me to remove the /setup skill? You won't need it again."

If yes:
```bash
rm -rf .claude/skills/setup
git add -A
git commit -m "chore: remove setup wizard (setup complete)"
```

If no: "No problem - it'll just sit here quietly. It won't trigger again since there are no more placeholders to fill."

---

## Key Principles

- **Batch questions, don't drip.** Two interview rounds, not twenty individual questions.
- **Explain where to find IDs.** Not everyone knows how to find a Slack channel ID or PagerDuty team ID. Give specific instructions.
- **Show before committing.** Always show the summary and get confirmation before the git commit.
- **Suggest next steps.** The setup is just the beginning - point them toward immediate-value actions.
- **Warm tone.** This is someone's first interaction with their team context. Make it feel welcoming, not bureaucratic.
