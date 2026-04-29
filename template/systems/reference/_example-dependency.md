<!-- last-reviewed: 2026-04-01 -->
<!-- EXAMPLE: Delete this file once you have real system docs -->
# Auth Gateway

> Company-wide authentication and authorization gateway. Validates tokens, enforces permissions.

## Overview
The Auth Gateway sits in front of all API traffic and handles token validation and permission checks. Our services depend on it for every authenticated request. We don't own it, but when it has issues, our services break.

## How Claude Works With This
| Action | How |
|--------|-----|
| Make changes | We don't - escalate to Platform Infra team |
| Investigate | Check Auth Gateway Datadog dashboard, or ask in #ask-platform-infra |
| Deploy | Owned by Platform Infra |

## What the Team Owns
- Nothing - we are consumers only
- We configure our services' permission scopes in our own repos

## What the Team Does NOT Own
- The gateway itself, its deployment, its configuration
- Token issuance and rotation policies
- Permission model definitions

## When It Breaks
- **Symptoms we see:** 401/403 errors across all our services, usually sudden onset
- **First check:** Auth Gateway Datadog dashboard - is it their issue or ours?
- **If it's them:** Post in `#ask-platform-infra` with the error pattern and timestamp
- **If it's us:** Check our service's token configuration and permission scopes
- **Common false alarm:** Token rotation - our services cache tokens for 15min, so there's a brief window during rotation where some requests fail. This is expected and self-resolves.

## Related Systems
- **Upstream:** Identity Provider (SSO, token issuance)
- **Downstream:** Every authenticated service (including all of ours)

## Pointers
- **Investment:** Reference only - we consume, don't contribute
- **Escalation:** `#ask-platform-infra`, on-call: `@platform-infra-oncall`
- **Dashboard:** Datadog > Auth Gateway Overview (read-only access)
