---
name: pm-story
description: "ALWAYS use this skill - do not call Monday MCP tools directly - when the user wants to write, document, update, or create a product story or task. Triggered by ANY of these: \"pm-story\", \"write a story\", \"document this task\", \"update this task\", \"fill in this task\", \"write up this task\", \"log this idea\", \"track this\", \"add this to the board\", \"open a task for X\", \"create a task\", \"add a task\", \"log an issue\", \"I found a bug\", \"track this issue\", \"add this to the sprint\", or any message that includes a Monday task URL with intent to document or fill in product context."
user_invocable: true
---

# PM Story

Two cases - detect from the user's message:

* **New task:** User has an idea with no Monday item yet. Create one.
* **Existing task:** User points to a URL or name. The item exists but is likely a placeholder with only a title.

In both cases, the output is always the same: a Monday item description with Why / What / Done When / Open Questions - grounded in actual system context, not guesswork.

The goal is to exhaust every available context source before asking the user anything.

---

## Step 1: Load the Task (existing tasks only)

If the user gives a URL or name, load the full Monday context in parallel:

* **Item detail** - `get_board_items_page({{MONDAY_TASKS_BOARD_ID}})` with the item ID, `includeItemDescription: true`, `includeColumns: true`
* **Item comments** - `get_updates` on the item ID
* **Linked epic** - if `task_epic` has a linked item, fetch it with `includeItemDescription: true`. Also fetch other tasks in the same epic (gives scope and what's being built around this).

If it's a new task, note what the user described and move to Step 2.

---

## Step 2: Identify the System

Keyword-match the task name and any description against the systems table in CLAUDE.md. Read the matching system file - this is mandatory. It contains repo names, service paths, and architecture context needed to ground the brief.

<!-- Customize this table with your team's systems. Examples: -->
<!-- Engineering: -->
<!-- | Widget, CRUD, webhooks | `systems/owned/widget-service.md` | -->
<!-- BI/Analytics: -->
<!-- | Dashboard, metrics, Looker | `systems/owned/analytics-dashboard.md` | -->
<!-- Sales: -->
<!-- | Pipeline, deals, Salesforce | `systems/owned/deal-pipeline.md` | -->

If the task spans multiple systems, read all relevant files. If nothing matches, read `README.md` and use the systems table there.

---

## Step 3: Saturate Context (all in parallel)

Use the system doc to find repos, service paths, and Slack channels. Then run all of these at once:

### 3a. Deep context (domain-specific)

**If the matching system doc has a `Repos` section** (engineering/data teams):
Use `Glob` and `Grep` on the local repo path (from the system doc) to find files related to the task. Read 2-4 relevant files. You are looking for **product understanding**, not implementation details:
* What does the feature currently do? What can users configure, see, or trigger?
* What are the current entry points / user-facing surfaces?
* Are there any comments, TODOs, or flags that hint at known gaps or planned changes?

Do NOT surface file names, function names, or code-level details in the brief. Translate what you find into product/user language.

**If the system doc has NO `Repos` section** (non-engineering teams):
Skip code search. Instead, increase the weight on:
* Monday board history: search for related items on the tasks board
* System doc content: extract more context from the "Known Issues", "Architecture", and "Key Entry Points" sections
* The goal is the same: exhaust available context before asking the user

### 3b. Git history (engineering/data teams only)

Skip this step if the system doc has no `Repos` section.

On the relevant files, run:
```bash
git -C <repo-path> log --oneline -20 -- <relevant-file-paths>
```
Use commit messages to understand what has changed recently. Translate into product context (e.g. "a new creation wizard was recently introduced"), not commit hashes.

### 3c. Slack
Search the relevant system channel and `#{{TEAM_SLACK_CHANNEL}}` for recent discussions about the task topic. Use `slack_search_public_and_private` with keywords from the task name.

This surfaces product decisions, user feedback, and open debates that aren't in code or Monday.

---

## Step 4: Section Walkthrough

Walk through each section with the user - one at a time. Don't dump a full draft upfront. For each section:
1. Share your read based on context
2. Ask if it's right and if anything is missing
3. Wait for confirmation or correction before moving on

Do this for **Why**, then **What**, then **Done When** - in that order.

Keep each message short. One section at a time. Use casual language throughout.

Example flow:

```
**Why - here's my read:**
[1-2 sentences from your research]

Sound right, or is there a different pain point driving this?
```

Wait for response, then:

```
**What - here's my read:**
[1-3 sentences on what changes]

Anything missing or different from what you had in mind?
```

Wait for response, then:

```
**Done When - draft:**
- [ ] [scenario]
- [ ] [scenario]
- [ ] [scenario]

Anything missing or wrong here?
```

Once all three sections are confirmed, move to Step 5 (write to Monday). Do NOT ask the user to confirm again - just write it.

---

## Step 5: Draft the Final Brief

Assemble the confirmed sections into the final brief. No need to show it again - go straight to writing to Monday.

---

## Step 6: Task Metadata (new items only, run in parallel with Step 4)

Skip if updating an existing item.

1. Call `get_sprints_metadata({{MONDAY_SPRINTS_BOARD_ID}})` to get the active and next sprint names and IDs.
2. Ask ALL of the following in a single `AskUserQuestion` call:

**Sprint** (default = active sprint):
- [Active sprint name] (default)
- [Next sprint name]
- Backlog (no sprint)

**Owner** (default = me):
- Me (default)
- Unassigned
- Someone else

**Priority** (default = Medium):
- Medium
- High
- Critical
- Best Effort

**System / Area** (optional - reads from the Systems Reference table in CLAUDE.md):
<!-- Systems are auto-populated from CLAUDE.md. If none match, use: -->
- Other / None

3. If owner = "Someone else": follow up with `list_users_and_teams` to look up their ID.
4. If owner = "Me": resolve the current user ID at write time via `get_user_context`.

<!-- System/Area item ID map - fill in with your Systems Catalog board item IDs -->
<!-- To find these: open your Systems Catalog board, click on each system item, the ID is in the URL -->
<!-- | System | Item ID | -->
<!-- |--------|--------| -->
<!-- | Widget Service | 12345678 | -->

<!-- Priority label ID map - discover from your board's column settings -->
<!-- Open your Tasks board > click Priority column header > "Column settings" > note the label IDs -->
<!-- | Label | ID | -->
<!-- |-------|----|-->
<!-- | Critical | 0 | -->
<!-- | Best Effort | 1 | -->
<!-- | High | 2 | -->
<!-- | Medium | 7 | -->

---

## Step 7: Write to Monday

**Task name (new items):** Generate a short, human-sounding name - 5-8 words max, lowercase except proper nouns. Infer it from the description; do NOT ask the user. Examples: `"fix widget sync failures"`, `"add metadata filters to API"`, `"move token generation outside settings page"`.

**New item** - call `create_item`:
* `boardId`: `{{MONDAY_TASKS_BOARD_ID}}`
* `name`: inferred short task name (see above)
* `columnValues`:
  * `task_type`: best fit from `Feature`, `Improvement`, `Bug Fix`, `Maintenance`, `Duty`, `Other`
  * `task_status`: `{"label": "Ready to start"}` (always set this on new items)
  * `task_sprint`: sprint item ID (omit if Backlog)
  * `task_owner`: `person-{user_id}` (omit if Unassigned)
  * `task_priority`: priority label id from map above

**Existing item** - use the existing item ID. No column changes needed.

**Set the description** via `all_monday_api` (pass `variables: "{}"`):

```graphql
mutation {
  set_item_description_content(item_id: "ITEM_ID", markdown: "MARKDOWN_CONTENT") {
    block_ids
  }
}
```

Template:

```markdown
## Why
{1-2 sentences max}

## What
{1-3 sentences max}

## Done When
- [ ] {scenario in plain language}
- [ ] {scenario}
- [ ] {scenario - 2-4 max}

## Open Questions
- {one line per question}
(omit if none)

---
*Documented via Claude Code on {today's date}*
```

---

## Step 8: Confirm

Return the item URL: `https://{{MONDAY_WORKSPACE}}.monday.com/boards/{{MONDAY_TASKS_BOARD_ID}}/pulses/{item_id}`

---

## Key Principles

* **Exhaust context before asking.** Monday, epic, siblings, system doc, code, git history, Slack - use all of it. The user should only fill what you genuinely can't find.
* **This is a product skill, not an engineering skill.** Read the code to understand what users experience today. Never surface file names, function names, component names, or technical internals in the brief. Translate everything into user-facing, business-facing language.
* **One output format, always.** Why / What / Done When / Open Questions. Nothing else.
* **Section walkthrough, not a full draft.** Walk through Why, What, Done When one at a time. Share your read, confirm with the user, then move on. Write to Monday only after all three are confirmed.
* **Casual tone, plain words.** Write like a teammate, not a spec writer. "Clean up" not "purge", "pick" not "configure". Short sentences.
* **Done When = checklist, not BDD.** Use `- [ ]` format. Plain language scenarios, no "Given/When/Then".
* **Batch questions, not drip questions.** Ask everything at once.
