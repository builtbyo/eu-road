# API contract baseline

## Principles

- JSON over HTTPS, resource-oriented routes, UTC ISO-8601 timestamps, UUID identifiers, and explicit pagination.
- Every authenticated request resolves a workspace context from the session plus an explicit workspace slug/ID where needed. The server verifies membership for each request.
- The API never exposes provider tokens, opaque internal errors, or another workspace's identifiers.
- Commands that can be retried accept an `Idempotency-Key` header. Background work returns a durable resource with observable state.
- Write endpoints return the canonical representation plus version/ETag semantics where concurrent edits matter.
- Breaking changes require a versioned endpoint/contract and migration plan. Additive fields are preferred.

## Error envelope

```json
{
  "error": {
    "code": "validation_failed",
    "message": "One or more fields are invalid.",
    "request_id": "019...",
    "details": [
      { "field": "target_role_id", "code": "required", "message_key": "validation.required" }
    ]
  }
}
```

Errors use stable machine-readable codes and localisable `message_key` values. Validation, authorization, conflict, rate-limit, dependency, and unexpected-error cases remain distinct.

## Initial resource surface

| Area | Routes (illustrative) |
| --- | --- |
| Identity | `POST /v1/auth/register`, `POST /v1/auth/login`, `GET /v1/me`, `POST /v1/auth/google/*`, `POST /v1/auth/logout` |
| Workspace | `GET /v1/workspaces`, `PATCH /v1/workspaces/{id}`, `GET/POST /v1/workspaces/{id}/members` |
| Profile | `GET/PATCH /v1/workspaces/{id}/profile`, `GET/POST/PATCH /experiences`, `/achievements`, `/skills`, `/certifications`, `/cv-snapshots` |
| Targeting | `GET/POST/PATCH /campaigns`, `/target-roles`, `/target-markets`, `/target-companies` |
| Pipeline | `GET/POST /applications`, `GET /applications/{id}`, `POST /applications/{id}/events`, `/materials`, `/tasks`, `/interviews`, `/offers` |
| Network | `GET/POST/PATCH /contacts`, `POST /contacts/{id}/interactions`, `POST /referrals` |
| Catalog | `GET /occupations`, `/skills`, `/companies`, `/job-postings`, `/visa-routes`; user writes never modify shared catalog directly |
| Growth | `POST /gap-assessments`, `GET /gap-assessments/{id}`, `GET/POST/PATCH /learning-plans` |
| AI | `POST /copilot/messages`, `GET /ai-proposals`, `POST /ai-proposals/{id}/approve`, `/reject`, `/edit-and-approve` |
| Privacy | `GET/PATCH /consents`, `POST /exports`, `POST /deletion-requests`, `GET /audit-events` |

## Application event command

`POST /v1/workspaces/{workspace_id}/applications/{application_id}/events` accepts an event, not a raw replacement status:

```json
{
  "event_type": "recruiter_screen_completed",
  "occurred_at": "2026-09-12T14:30:00Z",
  "source": "manual",
  "note": "Positive conversation; expected technical interview next week."
}
```

The server validates event transition policy, creates the immutable event, updates/rebuilds the projection, schedules relevant follow-up work, and returns both the event and current stage.

## AI proposal command

Copilot mutations are opaque to the model but explicit to the API. A proposal includes `action_type`, versioned structured `payload`, source references, risk classification, expiry, and status. Approval calls use the same domain command path as the UI; no model credential may directly mutate tables.

## Pagination and filtering

Collection endpoints use cursor pagination: `?limit=50&after=<opaque-cursor>`. Filters are explicit and documented per resource, with deterministic ordering. Full-text or semantic search responses state which mode was used and do not treat ranking as a factual claim.
