---
name: retro
description: Use this skill when the user wants to capture learnings from a completed workflow, save knowledge for next time, update a skill or system doc based on what was discovered, or reflect on what went well/poorly. Triggered by "let's retro", "capture what we learned", "save this for next time", "update the skill", "what did we learn", "document this for next time", "don't forget this", "we should remember this". Also suggest this skill proactively after completing a novel or complex workflow.
---

# Retro - Capture and Commit Learnings

This skill closes the flywheel loop: after completing a novel workflow, it captures what was learned and commits it to the repo so the whole team benefits. Without this step, knowledge stays in one person's head (or worse, in memory where only one user sees it).

**When to use retro vs curious-intern:** Retro is reactive - something just happened, capture it quick (2 min). Curious-intern is proactive - you have spare time, let Claude interview you to systematically fill gaps (15-30 min). If the user has more than a quick learning to capture, suggest `/curious-intern` instead.

## Step 1: Interview

Ask the user (batch these, don't drip):

1. **What did we just do?** (one sentence - which system, what workflow)
2. **What was surprising or non-obvious?** (the stuff a teammate doing this next time would trip on)
3. **What would you want Claude to know next time?** (the shortcut, the gotcha, the "don't do X")
4. **Did any existing skill or doc give wrong/stale information?** (if yes, which one)

If the conversation history makes any of these obvious, skip that question and state your assumption for confirmation.

## Step 2: Route the Learning

Based on the answers, determine where each piece of knowledge belongs:

| What was learned | Where it goes | Action |
|-----------------|---------------|--------|
| System behavior, API quirk, failure mode | `systems/owned/<system>.md` | Add to "Known Issues / Failure Modes" or relevant section |
| New skill workflow or improvement to existing skill | `.claude/skills/<name>/SKILL.md` or `knowledge/` | Update the skill instructions or add a knowledge file |
| Missing step in a workflow, wrong assumption | `.claude/skills/<name>/SKILL.md` | Fix the instruction |
| Team convention or workflow rule | `CLAUDE.md` or `references/` | Add to the appropriate section |
| Stale information (wrong board ID, dead URL, renamed service) | The file that contains the stale info | Fix it directly |
| User's personal preference (style, tone, approach) | Memory | Save to memory - this is the ONLY case for memory |

**Important:** If you're tempted to save something to memory, ask yourself: "Would this help OTHER team members too?" If yes, it belongs in the repo.

## Step 3: Make the Changes

1. Read the target file(s) to understand current content
2. Make the edit - add the learning in the appropriate section, following the file's existing structure
3. If adding to a system doc, update the `<!-- last-reviewed: YYYY-MM-DD -->` date
4. If the learning is substantial enough to warrant a PR (new section, new knowledge file, significant update), use `/pr-ship` to create one
5. If it's a small fix (typo, stale URL, one-line addition), just make the edit directly

## Step 4: Confirm

Show the user what was captured and where:

```
Captured learnings:

1. Added failure mode "X causes Y" to systems/owned/airflow.md
2. Updated skill step 3 in .claude/skills/airflow-debug/SKILL.md to include Z
3. Fixed stale board ID in references/monday_boards.md

These changes are [committed / ready for PR via /pr-ship].
```

## When to Suggest This Skill Proactively

After completing any workflow where:
- You had to be guided through steps that weren't in a skill
- You discovered a system behavior that isn't documented
- An existing skill gave wrong or incomplete instructions
- The user corrected you on something that other users would also need to know

Say: "We learned some things during this workflow. Want to run `/retro` to capture them before they're lost?"

## Key Principles

- **Repo over memory.** System knowledge in memory is a bug, not a feature. Only user preferences go to memory.
- **Small, frequent updates beat big documentation pushes.** Don't wait for a "documentation sprint" - capture learnings immediately while context is fresh.
- **Update the `last-reviewed` date** on any system doc you touch. This keeps `/health-check` accurate.
- **Don't duplicate.** Before adding something, check if it's already documented elsewhere. If it is, update the existing content rather than adding a second copy.
