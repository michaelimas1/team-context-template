# Systems Directory

## What Is a System?

A **system** is anything your team operates, maintains, or depends on to do its work. It could be a service, a platform, a tool, a workflow, or a dataset.

| Team type | Example owned systems | Example reference systems |
|-----------|----------------------|--------------------------|
| Engineering | API service, data pipeline, CI/CD, feature flags | Auth gateway, cloud provider, monitoring |
| Data / BI | Looker instance, dbt project, Snowflake schemas | Source databases, eng pipelines |
| Sales | Salesforce instance, deal pipeline, quoting tool | Product catalog, billing system |
| Marketing | HubSpot instance, content CMS, campaign tracker | Analytics dashboards, product data |
| HR | BambooHR, recruiting pipeline, onboarding workflow | IT provisioning, payroll system |
| Finance | Budget tracker, forecasting model, expense system | ERP, procurement, billing |

Maps of systems relevant to your team. Split into two categories:

- **`owned/`** - systems the team owns and operates
- **`reference/`** - systems you integrate with or depend on but don't own

These are **maps, not manuals** - detailed docs and skills live in each system's own repo. Only add what's useful to know regardless of which codebase you're in.

## Owned Systems

Systems the team is responsible for building, operating, or maintaining.

<!-- Add your systems here. Group by domain if you have many. Example: -->
<!-- | System | Criticality | Notes | -->
<!-- |--------|-------------|-------| -->
<!-- | [Service Name](owned/service-name.md) | High | Brief note | -->

## Reference Systems

Systems you integrate with or depend on but don't own.

<!-- Add systems you depend on. Example: -->
<!-- | System | Owner | Why it matters | -->
<!-- |--------|-------|----------------| -->
<!-- | [External Service](reference/external-service.md) | Other Team | Brief explanation | -->

## Templates

Every system doc must include a `<!-- last-reviewed: YYYY-MM-DD -->` comment on the **first line of the file** (before the `# Title`). This is checked by `/health-check`.

### Full Template (Active Investment / Active Growth / Critical)

Use for systems with active development, high criticality, or significant operational surface.

```markdown
<!-- last-reviewed: 2026-03-31 -->
# System Name

> One-liner description

## Overview
What this system does and why it matters (2-3 sentences).

## How Claude Works With This
<!-- Examples by team type:
Engineering: Make changes = GitHub PRs | Investigate = Datadog, logs | Deploy = CI/CD on merge
BI/Analytics: Make changes = Edit LookML/dbt | Investigate = Query warehouse | Deploy = N/A (auto-refresh)
Sales: Make changes = Salesforce admin UI | Investigate = Reports, dashboards | Escalate = Salesforce admin team
Marketing: Make changes = HubSpot/CMS admin | Investigate = Campaign analytics | Deploy = Publish/schedule
-->
| Action | How |
|--------|-----|
| Make changes | GitHub PRs to `your-org/repo-name` |
| Investigate | Skill name, MCP tool, or Datadog |
| Deploy | CI/CD, git-sync, Terraform, or manual |

## Repos / Data Sources
<!-- For engineering: list code repos. For BI: list data sources and warehouse schemas. 
     For other teams: list the tools and platforms where this system's configuration lives. -->
| Repo | What it contains |
|------|-----------------|
| `your-org/repo-name` | Brief description |

## Architecture
How the pieces connect - text description or ASCII diagram.

## Key Entry Points
<!-- For engineering: API routes, config files, cron jobs. 
     For BI: key dashboards, reports, scheduled queries.
     For sales/marketing: key workflows, automation rules, report views. -->
- Where to look when X happens
- Where config lives
- Where alerts fire

## Known Issues / Failure Modes
| Issue | Symptoms | Resolution |
|-------|----------|------------|
| Description | What you see | What to do |

## Related Systems
- **Upstream:** what feeds into this
- **Downstream:** what depends on this

## Pointers
- **Skills:** which skills exist (in this repo or code repos)
- **Dashboards:** links to Datadog/Grafana boards
- **Alerts:** where alerts fire and who gets paged
```

### Light Template (Maintain-Only)

Use for systems where the team owns infrastructure but not feature development. Shorter, focused on "what breaks and who to call."

```markdown
<!-- last-reviewed: 2026-03-31 -->
# System Name

> One-liner description

## Overview
What this system does and why the team is involved (1-2 sentences).

## How Claude Works With This
| Action | How |
|--------|-----|
| Make changes | K8s config, admin UI, or Terraform |
| Investigate | Pod health, Datadog, admin panel |
| Deploy | K8s rollout or manual |

## What the Team Owns
- Infrastructure, uptime, deployment

## What the Team Does NOT Own
- Content, feature development (owned by X team)

## When It Breaks
- Where to check first (logs, dashboards, pods)
- Common failure modes and fixes
- Who to escalate to if it's not infra

## Related Systems
- **Upstream:** what feeds into this
- **Downstream:** what depends on this

## Pointers
- Investment: Maintain Only
- Escalation: team/channel for non-infra issues
```

### Compliance Checklist

Before merging a system doc PR, verify:
- [ ] Has `<!-- last-reviewed: YYYY-MM-DD -->` on first line of the file
- [ ] Follows the correct template (full or light based on investment state)
- [ ] Has "How Claude Works With This" section with make changes / investigate / deploy
- [ ] No TODO or placeholder comments left unfilled
- [ ] Pointers section has actual links (not empty bullets)
- [ ] Related Systems lists upstream and downstream

## Examples

See `systems/owned/_example-active-system.md` (engineering), `_example-analytics-system.md` (BI), and `_example-sales-system.md` (sales) for domain-specific examples.
