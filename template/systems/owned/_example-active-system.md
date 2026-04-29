<!-- last-reviewed: 2026-04-01 -->
<!-- EXAMPLE: Delete this file once you have real system docs -->
# Widget Service

> Manages widget lifecycle (CRUD, webhooks, bulk operations) for the platform.

## Overview
The Widget Service is the core backend for all widget operations. It handles creation, updates, deletion, and webhook notifications for ~2M widgets across all accounts. It's the most-trafficked service the team owns, processing ~50K requests/minute during peak hours.

## How Claude Works With This
| Action | How |
|--------|-----|
| Make changes | GitHub PRs to `acme-corp/widget-service` |
| Investigate | Datadog dashboard, pod logs, Snowflake query history |
| Deploy | Auto-deploy on merge to main via CI/CD |

## Repos
| Repo | What it contains |
|------|-----------------|
| `acme-corp/widget-service` | API server, webhook dispatcher, bulk operation workers |
| `acme-corp/widget-sdk` | Client SDK used by other services to interact with widgets |

## Architecture
```
Client -> API Gateway -> Widget API (K8s pods, 3 replicas)
                              |
                              +-> PostgreSQL (primary data store)
                              +-> Redis (caching, rate limiting)
                              +-> Webhook Dispatcher (async, SQS queue)
                              +-> Bulk Worker (async, processes large operations)
```

## Key Entry Points
- API routes: `src/routes/` - all widget CRUD endpoints
- Webhook dispatcher: `src/workers/webhook-dispatcher.ts` - processes webhook queue
- Bulk operations: `src/workers/bulk-processor.ts` - handles imports/exports
- Config: `src/config/` - feature flags, rate limits, queue settings

## Known Issues / Failure Modes
| Issue | Symptoms | Resolution |
|-------|----------|------------|
| Webhook queue backup | Webhook deliveries delayed >5min, SQS queue depth spikes | Scale up webhook dispatcher workers. If persistent, check for a single account flooding the queue |
| Redis connection exhaustion | 503 errors on read endpoints, "max connections reached" in logs | Restart pods (rolling). Root cause is usually a connection leak during bulk operations |
| Bulk import OOM | Bulk worker pods crash with OOM killed | Reduce batch size in config. Imports >100K widgets need chunking |

## Related Systems
- **Upstream:** Account Service (provides account context), Auth Gateway (validates tokens)
- **Downstream:** Analytics Pipeline (consumes widget events), Notification Service (triggered by webhooks)

## Pointers
- **Skills:** None yet - create one for debugging widget issues!
- **Dashboards:** Datadog > Widget Service Overview
- **Alerts:** PagerDuty, routed through `widget-service` integration
