---
name: good-morning
description: Use this skill when the user says "good morning", "morning brief", "daily brief", "daily standup", "catch me up", "what happened overnight", "what's going on today", "start of day", or wants a daily summary of their tasks, team PRs, and Slack activity. Generates a structured morning brief covering active sprint tasks with burndown, unowned bugs, key Slack highlights, PRs open for review, and PRs merged in the last 24 hours.
---

# Good Morning Brief

Generate a daily morning brief for the team member. The goal is to give them everything they need to orient themselves in 60 seconds - their own tasks, what the team shipped, what needs review, and what's happening in Slack.

Fetch ALL data in parallel before rendering anything. Speed matters here - this is the first thing someone does in the morning.

---

## Step 1: Read Source Registry and Fetch Everything in Parallel

Read the **Morning Brief Sources** table from `CLAUDE.md`. For each source where Enabled = "Yes" (not commented out), launch the corresponding fetch in parallel. Skip sources that are commented out or not present.

### Source: Sprint tasks (monday)
Use `mcp__monday-api__get_sprints_metadata` with the sprints board ID from the config to find the ACTIVE sprint. Then use `mcp__monday-api__get_board_items_page` on the tasks board.

Configure the filters and columns based on your team's board schema in `references/monday_boards.md`. At minimum:
- Filter to tasks owned by the current user
- Filter to the active sprint only (typical patterns: a sprint linker column, or a mirrored sprint-status column)
- Fetch the columns you want shown in the brief (typically: owner, status, priority, type, estimation, name)

### Source: Unowned bugs (monday)
Same board, different filter: bugs in the active sprint with no owner. Look up your "Bug Fix" label ID from `references/monday_boards.md` and filter on the task type column.

### Source: Slack (slack)
```
mcp__plugin_slack_slack__slack_read_channel(channel_id: "<channel_id_from_config>", limit: 50, response_format: "concise")
```

<!-- Add more Slack channels here if your team monitors additional channels. Example: -->
<!-- mcp__plugin_slack_slack__slack_read_channel(channel_id: "OTHER_CHANNEL_ID", limit: 15, response_format: "detailed") -->

### Source: Open PRs (github)
Only if enabled in the source registry. For each repo listed in the config:
```bash
gh pr list --repo <org>/<repo> --state open \
  --json number,title,author,createdAt,reviewRequests,isDraft \
  | jq '[.[] | select(.isDraft == false) | select(.author.is_bot == false)] | .[:5]'
```

**Team GitHub logins** (for filtering shared repos): Read the `GitHub logins` field from the Team section in CLAUDE.md (pipe-separated list, e.g. `user1|user2|user3`).

**Bot/noise exclusions**: `dependabot` and any `is_bot == true` accounts. Add team-specific CI bot logins here as needed.

### Source: Merged PRs (github)
Only if enabled in the source registry. Compute the cutoff as 6 AM UTC yesterday:
```bash
CUTOFF=$(date -u -v-1d '+%Y-%m-%dT06:00:00Z' 2>/dev/null || date -u -d 'yesterday 06:00' '+%Y-%m-%dT06:00:00Z')
gh pr list --repo <org>/<repo> --state merged --limit 50 \
  --json number,title,author,mergedAt \
  | jq --arg cutoff "$CUTOFF" '[.[] | select(.author.is_bot == false) | select(.mergedAt > $cutoff)]'
```

### Source: PagerDuty (pagerduty)
Only if enabled in the source registry. Fetch both in parallel:
```
mcp__plugin_pagerduty_pagerduty__list_incidents(
  teams_ids: ["<team_id_from_config>"],
  since: <now minus 24h as ISO8601>,
  sort_by: ["created_at:desc"],
  limit: 50
)

mcp__plugin_pagerduty_pagerduty__list_oncalls(
  escalation_policy_ids: ["<escalation_id_from_config>"],
  earliest: true
)
```
Fetch ALL incident statuses (triggered, acknowledged, resolved).

---

## Step 2: Render the Brief

**Important:** Only render sections for sources that were enabled and fetched. If a source is not in the Morning Brief Sources table (or is commented out), skip that entire section silently. Do not show empty sections or "not configured" messages.

Use this exact structure. Keep it tight - this is a morning brief, not a report.

### Header
```
# Good Morning Brief - [Weekday, Month DD, YYYY]
```

---

### Sprint [N] - Your Tasks

Show the sprint timeline and task burndown as a visual battery chart:

```
Sprint timeline  [bar]  [X]%  (day N/total)
Your tasks done  [bar]  [X]%  ([done] done, [in-review] pending review)
```

Sprint percentage = (current day / sprint total days). Task percentage = done tasks / total tasks (count Pending Review as ~50% toward done for visual purposes, but note them separately in the label).

Then a table of tasks:

| Task | Status | Priority | SP |
|------|--------|----------|----|
| [name](url) | emoji + label | label | N |

Status emojis: In Progress, Pending Review, Ready to start, Done, Stuck

One short insight below the table (1-2 sentences max). Flag if too many things are "In Progress" simultaneously, or if high-priority tasks haven't been touched. Keep it actionable, not preachy.

---

### Unowned Bugs

If none: "> No unowned bugs in the active sprint. Nice."

If any, a table:
| Bug | Age |
|-----|-----|
| [name](url) | X days |

Note: if the bug has been sitting for >7 days, flag it - it needs an owner or a close decision.

---

### Slack Highlights

**#{{TEAM_SLACK_CHANNEL}}** (last 48h)

Pull only the last 48 hours of messages. List 3-5 key items max. Each item is one line:
- Lead with an emoji that fits the vibe
- Name the person
- One-sentence summary
- If it's an open question or needs a response, add **unanswered** or **needs decision** in bold at the end

---

### PRs Open for Review

Single combined table across all repos, sorted by age ascending (newest first):

| # | Repo | Title | Author | Age |
|---|------|-------|--------|-----|
| [#N](url) | repo-name | title | Name | 3d |

- Mark your own PRs with **you** - those need someone else's review.
- Flag PRs older than 7 days.
- Skip bots and non-team contributors (use the GitHub login allowlist above).

If no team PRs open: "> No open team PRs right now."

---

### Merged Yesterday

Narrative format, grouped by system area. Last 24 hours, team only.

For each area with merged PRs, write a header with an emoji, then one bullet per person who merged something. Link PR numbers inline (e.g. [#123](url)). Highlight if it was you with **you**.

<!-- Customize these system groupings for your team. Examples: -->
<!-- **Backend** - API changes, service updates -->
<!-- **Frontend** - UI, client-side changes -->
<!-- **Infrastructure** - CI/CD, config, tooling -->
<!-- **Team** - team context, skills, docs -->

One line at the bottom: "N merges across the team yesterday."

If none: "> No team merges in the last 24 hours."

---

### PagerDuty - Last 24 Hours

Start with who is currently on-call:
```
On-call: **[Name]** (until [date])
```

If on-call data is unavailable, omit the line rather than showing an error.

Then show a table of all incidents from the last 24 hours, sorted newest first:

| # | Status | Title | Service | Assigned To | Age |
|---|--------|-------|---------|-------------|-----|
| [#N](url) | status | short title | service name | Name | 2h |

- PagerDuty incident URL pattern: `https://{{PAGERDUTY_SUBDOMAIN}}.pagerduty.com/incidents/<id>`
- If no incidents in the last 24h: "> No incidents in the last 24 hours."

One line summary below the table: "X open, Y acknowledged, Z resolved in the last 24h."

---

### Suggested Actions

After rendering the full brief, present 3-5 concrete action items derived from the data. Use `AskUserQuestion` with `type: "select"` to let them pick what to act on first.

Options should be specific and actionable, pulled directly from the brief. Include a "Nothing, I'm good" option as the last choice. If the user picks something, help them act on it immediately.

---

## Key Principles

- **Speed over completeness.** Fetch everything in parallel. Don't paginate Slack unless the first page gives almost nothing.
- **Team-only GitHub data.** If PRs are enabled, use the GitHub login allowlist strictly. A non-team PR in the table is confusing and wrong.
- **48h for Slack, since yesterday morning (6 AM UTC) for merged PRs, most recent 5 for open PRs.** Only applies to enabled sources.
- **One line per Slack item.** If you can't summarize it in one line, it's not important enough for the morning brief.
- **No padding.** If a section is empty, say so cleanly and move on. Don't write "I was unable to find any..." - just say "No unowned bugs this sprint."
- **Emoji in section headers** makes the brief scannable at a glance. Use them.
