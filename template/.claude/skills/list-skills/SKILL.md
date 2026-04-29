---
name: list-skills
description: Use this skill when the user asks to "list skills", "show skills", "what skills are available", "available skills", "skills list", "skill shortcuts", "show shortcuts", "what can you do", "what skills do we have", "show me the skills", or wants to see an overview of all team skills and how to trigger them. Always use this skill instead of summarizing from memory - the list is read live from the skills directory.
---

# List Available Skills

Show all skills available in this repo with their trigger phrases and a one-line summary of what each does.

## How to Generate the List

1. Use Glob to find all skill files: `.claude/skills/*/SKILL.md`
2. Read each file's YAML frontmatter (the `---` block at the top) to extract:
   - `name` - the skill identifier
   - `description` - contains trigger phrases (quoted strings like "do X") and what the skill does
3. For each skill, extract:
   - **Trigger phrases**: the quoted strings from the description (e.g. "debug DAG", "failed DAG")
   - **One-line summary**: what the skill does, distilled to a single sentence from the description body

## Output Format

Present results as a clean markdown table grouped by domain. Use this structure:

```
## Available Skills

### Daily Operations
| Skill | Say something like... | What it does |
|-------|-----------------------|--------------|
| `good-morning` | "good morning", "morning brief" | Daily brief with sprint tasks, team updates, and action items |
...

### Knowledge & Learning
| Skill | Say something like... | What it does |
|-------|-----------------------|--------------|
| `curious-intern` | "interview me", "fill gaps" | Knowledge extraction interview to fill doc gaps |
| `retro` | "let's retro", "capture what we learned" | Captures learnings and commits them to the repo |
...

### Task Management
...

### System-Specific
...
```

Group skills into domains based on what they do (use judgment for fit). Some generic groupings that work across team types:

- **Daily Operations** - morning briefs, standups, status checks
- **Knowledge & Learning** - interviews, retros, health checks, onboarding
- **Task Management** - task creation, sprint workflows, board processing
- **System-Specific** - skills tied to a particular system the team owns

If the team has custom skills that don't fit these, create new domain groups as needed.

## Style Notes

- Keep trigger phrases to 2-4 of the most natural-sounding examples - don't list all of them
- The "What it does" column should be one crisp sentence, no jargon
- After the table, add a footer note: "To trigger a skill, just describe what you need - exact phrases aren't required."
- Include the `list-skills` skill itself in the table under the Knowledge & Learning group, so users know how to find this list again
