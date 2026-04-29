<!-- last-reviewed: 2025-04-01 -->
<!-- EXAMPLE: Delete this file once you have real system docs -->
# Customer Analytics Dashboard

> Looker-based dashboard suite tracking customer behavior, retention, and product adoption metrics.

## Overview

The Customer Analytics Dashboard is the team's primary self-serve analytics surface. It provides product managers, CS leads, and execs with real-time visibility into customer health - activation rates, feature adoption, churn risk, and NPS trends. Around 80 daily active users across the company rely on these dashboards for decisions ranging from quarterly planning to individual account interventions.

Key metrics tracked: activation rate, 7/30-day retention, feature adoption by tier, NPS score, churn probability, expansion revenue.

## How Claude Works With This

| Action | How |
|--------|-----|
| Make changes | Edit LookML models in `OrgName/looker-models` repo |
| Investigate | Looker admin panel, explore logs, Snowflake query history |
| Deploy | N/A - automatic on merge to main via Looker CI |

## Data Sources

| Source | Table / Dataset | What it provides |
|--------|----------------|-----------------|
| Snowflake | `analytics.core.fct_user_events` | Raw user interaction events |
| Snowflake | `analytics.core.dim_accounts` | Account attributes, plan tier, ARR |
| Snowflake | `analytics.core.fct_daily_active_usage` | Pre-aggregated DAU/WAU/MAU metrics |
| Snowflake | `analytics.core.dim_features` | Feature catalog with tier mapping |
| Salesforce (via Fivetran) | `raw.salesforce.opportunity` | Revenue data for expansion metrics |
| Segment (via warehouse sync) | `raw.segment.tracks` | Product event stream for adoption funnels |

All data lands in Snowflake's `ANALYTICS_WH` warehouse. The Looker connection uses `LOOKER_SVC` service account with read-only access.

## Architecture

```
Product App -> Segment -> Snowflake (raw.segment)
Salesforce  -> Fivetran -> Snowflake (raw.salesforce)
                                |
                           dbt models
                                |
                     Snowflake (analytics.core)
                                |
                         Looker (LookML)
                                |
                  +-------------+-------------+
                  |             |             |
           Exec Summary   CS Health    Product Adoption
            Dashboard     Dashboard      Dashboard
```

## Key Dashboards

| Dashboard | Audience | Refresh cadence |
|-----------|----------|----------------|
| Executive Summary | Leadership, board prep | Daily, 6am UTC |
| CS Account Health | CS managers, AEs | Hourly |
| Product Adoption | Product managers | Daily, 6am UTC |
| Retention Cohorts | Growth team | Weekly (Monday) |
| NPS Deep Dive | CX team | Daily after survey sync |

All dashboards are in the **Customer Analytics** Looker folder. Scheduled PDF deliveries go to `#analytics-weekly` Slack channel every Monday.

## Known Issues / Failure Modes

| Issue | Symptoms | Resolution |
|-------|----------|------------|
| Stale data from dbt job failure | Dashboards show yesterday's date in "last updated" footer, metrics look flat | Check the `analytics_core` dbt job in Airflow. Usually a Snowflake warehouse timeout - re-run the job |
| Broken joins after schema change | Dashboards show NULL or unexpected zeros for a metric | Someone changed a column in an upstream dbt model without updating the LookML. Check `fct_user_events` and `dim_accounts` for schema drift |
| Looker cache serving old results | Numbers don't match a direct Snowflake query | Clear the Looker cache for the affected explore via Admin > Queries > Clear Cache. This happens after large backfills |
| Permission errors on new dashboards | Users get "You don't have access" when clicking a dashboard link | Dashboard was created in a personal folder instead of the shared Customer Analytics folder. Move it and re-share |
| Segment event delay | Adoption metrics lag by several hours | Check Segment's delivery status page. If the warehouse sync is backed up, events arrive late. Usually self-resolves within 4 hours |

## Related Systems

- **Upstream:** Segment (product events), Salesforce (revenue data), Fivetran (connectors), dbt (transformations)
- **Downstream:** CS team (account reviews), Product team (feature prioritization), Finance (ARR reporting), exec weekly reports

## Pointers

- **Looker instance:** `analytics.company.com`
- **LookML repo:** `OrgName/looker-models`
- **Alert channel:** `#analytics-alerts` - Looker error notifications and dbt failures
- **Team channel:** `#analytics-team`
- **dbt docs:** `dbt-docs.company.com` - column-level lineage for all analytics models
