<!-- last-reviewed: 2025-04-01 -->
<!-- EXAMPLE: Delete this file once you have real system docs -->
# Deal Pipeline - Salesforce

> Salesforce instance managing deal stages, opportunity tracking, and revenue forecasting for the sales org.

## Overview

The Deal Pipeline is the single source of truth for all revenue-generating activity. It tracks every deal from first discovery call through close, powers the weekly forecast, and feeds downstream systems like billing and CS handoff. The entire sales org (~120 reps, 15 managers, VP+) lives in this system daily. Finance and RevOps rely on it for board-level reporting.

Key workflows: opportunity management, pipeline reviews, forecast submissions, territory assignment, CS handoff on close-won.

## How Claude Works With This

| Action | How |
|--------|-----|
| Make changes | Salesforce admin (config, flows, fields) or code PRs for Apex triggers |
| Investigate | Salesforce reports/dashboards, SOQL queries via dev console |
| Escalate | Salesforce admin team via `#salesforce-admin` |

**Important:** Most changes require a Salesforce admin. Claude can help draft SOQL queries, analyze pipeline data, and prepare change requests, but cannot directly modify Salesforce configuration.

## Key Objects

| Object | What it represents | Key fields |
|--------|--------------------|-----------|
| Opportunity | A deal in the pipeline | Stage, Amount, Close Date, Owner, Forecast Category |
| Account | A company we sell to or have sold to | Industry, ARR, Tier, CSM Owner |
| Contact | Individual people at accounts | Role, Decision Maker flag, Last Activity |
| Lead | Inbound prospects not yet qualified | Source, Status, Score, SDR Owner |
| Quote | Formal pricing sent to prospect | Line Items, Discount %, Approval Status |
| Task / Activity | Calls, emails, meetings logged against deals | Type, Due Date, Completed flag |

## Deal Stages

```
Discovery -> Qualification -> Demo/Eval -> Proposal -> Negotiation -> Closed Won
    |             |              |            |             |              |
    +-> Lost      +-> Lost       +-> Lost     +-> Lost      +-> Lost       +-> Handoff to CS
                                                                           +-> Billing trigger
```

| Stage | Forecast weight | Exit criteria |
|-------|----------------|---------------|
| Discovery | 10% | Pain point identified, budget holder known |
| Qualification | 25% | MEDDIC complete, champion confirmed |
| Demo/Eval | 50% | Technical win, positive eval feedback |
| Proposal | 70% | Pricing sent, no outstanding blockers |
| Negotiation | 90% | Legal/procurement engaged, redlines in progress |
| Closed Won | 100% | Contract signed, PO received |

## Known Issues / Failure Modes

| Issue | Symptoms | Resolution |
|-------|----------|------------|
| Duplicate accounts | Same company appears multiple times, contacts split across dupes | Run the weekly dedupe report (Reports > Data Quality > Duplicate Accounts). Merge via Salesforce admin - always keep the account with more activity history |
| Stale opportunities | Deals sitting in same stage for 60+ days with no activity | RevOps flags these in the weekly pipeline hygiene report. Reps need to update or mark as closed-lost. If systematic, check if a flow that auto-updates "Last Activity" is broken |
| Forecast drift | Submitted forecast doesn't match pipeline math | Usually caused by reps overriding forecast category manually. Compare Forecast Category vs Stage-implied category in the Forecast Accuracy report |
| Close date pushing | Deals repeatedly push close date forward without stage change | Symptom of sandbagging or stuck deals. Surface in pipeline review - if widespread, the stage exit criteria may need tightening |
| CS handoff failure | Closed-won deal doesn't trigger CS onboarding | The "Closed Won" Salesforce Flow that creates the CS handoff task may have failed silently. Check Flow Execution History in Setup. Common cause: missing required field on the Account (usually CSM Owner) |
| Lead routing delays | New leads sit unassigned for hours | Check the lead assignment rules in Setup > Lead Assignment Rules. Usually a territory mapping gap where the lead's region doesn't match any rule |

## Related Systems

- **Upstream:** Marketing automation (HubSpot - sends MQLs as Leads), website (form fills create Leads via Zapier)
- **Downstream:** Billing (Closed Won triggers subscription creation), CS platform (handoff task + account data), Data warehouse (Fivetran syncs nightly for analytics)
- **Adjacent:** CPQ (quote generation), DocuSign (contract signatures), Clari (forecast rollup)

## Pointers

- **Salesforce instance:** `company.my.salesforce.com`
- **Key reports folder:** Reports > Sales Pipeline (shared folder with all standard pipeline reports)
- **Forecast dashboard:** Dashboards > Revenue Forecast (updated live)
- **Admin channel:** `#salesforce-admin` - for config changes, new fields, flow fixes
- **RevOps channel:** `#revops` - for process questions, territory changes, forecast methodology
- **Data sync:** Fivetran connector syncs all objects nightly at 2am UTC to `raw.salesforce` in Snowflake
