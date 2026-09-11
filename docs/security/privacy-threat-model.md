# Privacy and threat model

## Scope

EU Road handles highly sensitive career data: contact details, CVs, employment history, job-search activity, interview notes, compensation, work-authorization data, and optional mailbox/calendar information. Production data will be hosted in an EU region initially. This document establishes the minimum design posture; it is not a completed legal or security certification.

## Data classification

| Class | Examples | Handling |
| --- | --- | --- |
| Restricted | Password hashes, OAuth refresh tokens, integration credentials, session secrets | Envelope encryption, least privilege, never logged/returned, rotation and revocation. |
| Sensitive personal | CVs, contact data, immigration/work-authorisation data, interview notes, salary, messages | Workspace access control, encryption, minimised processing, audit sensitive actions, retention/deletion controls. |
| Internal | Observability metadata, job state, source parser diagnostics | Access-controlled, redacted, bounded retention. |
| Public/catalog | Public company, role, course, and source metadata | Provenance, licence/terms review, freshness policy. |

## Assets and trust boundaries

1. Browser ↔ frontend: authenticated session boundary and XSS/CSRF boundary.
2. Frontend ↔ API: the API is the only authority for access and writes.
3. API/worker ↔ PostgreSQL and object storage: service identity and least-privilege boundary.
4. Integration provider ↔ connection credential store: OAuth credential, webhook verification, data minimisation, and revocation boundary.
5. API ↔ AI provider: prompt minimisation, redaction, retention, and tool-authority boundary.
6. Workspace A ↔ workspace B: strict tenant-isolation boundary, including direct IDs, search, cache, vector search, exports, and analytics.

## Principal threats and controls

| Threat | Controls |
| --- | --- |
| Cross-workspace data access | Workspace-scoped query repositories, membership checks, composite ownership constraints, negative authorization tests, scoped cache keys. |
| Account takeover | Verified email, secure password hashing, OAuth PKCE/state/nonce, anti-account-linking checks, session rotation/revocation, login throttling. |
| Token/secret exposure | Secret manager, envelope encryption, redacted logs, short-lived signed URLs, no browser exposure, provider credential rotation. |
| Malicious upload | MIME/content sniffing, AV scanning, size/type limits, quarantined assets, signed private storage, safe document rendering. |
| XSS/CSRF/session theft | Strict CSP, output encoding, `HttpOnly`/secure cookies, CSRF protection, origin checks, dependency updates. |
| Integration abuse or stale data | OAuth least scopes, source policy, request limits, webhook signature verification, expiration/freshness status, disconnect deletes/revokes token. |
| AI prompt injection or unwanted action | Treat external/provider text as untrusted, tool allowlists, structured proposals, approval enforcement, no implicit tool authority, adversarial tests. |
| Re-identification in benchmarks | Explicit opt-in, de-identification, minimum five contributors, aggregation thresholds, suppression and review. |
| Incorrect visa/sponsor information | Dated primary/authoritative sources where available, evidence confidence, recheck policy, visible uncertainty, no guarantees. |
| Data loss or corrupt migration | Automated backups, restore testing, migration review, immutable asset/version checks, event idempotency. |

## Consent and user rights

- Consent purposes are independent: marketing, AI processing configuration, public profile, email/calendar connection, aggregate benchmark contribution, and optional research.
- Each consent records notice version, actor, time, scope, and withdrawal. Withdrawal prevents future processing and triggers the relevant disconnect/purge workflow where applicable.
- Users can export personal data in a documented portable format and request deletion. Deletion runs as a durable, auditable process that removes or anonymises user-owned records according to the approved retention policy.
- Shared aggregates are computed only from opted-in contribution records and are suppressed below the minimum contributor threshold.

## Retention baseline to approve before beta

| Data | Proposed policy |
| --- | --- |
| Active workspace data | Retain while account/workspace is active. |
| Deleted workspace | Immediate logical lock; purge/anonymise asynchronously after the published grace period. |
| OAuth tokens | Delete/revoke on disconnect; never retain a usable token after consent withdrawal. |
| Email/calendar raw content | Store the minimum needed and define a short, explicit retention period before enabling integration. |
| Audit/security events | Retain for an approved security/legal period with access controls and redaction. |
| Backups | Encrypted, access-controlled, and expired on a documented schedule; deletion may complete when backup retention elapses. |

## Security release gates

- Threat model review is updated for every new integration, sensitive data category, or external action.
- Automated tests prove tenant isolation, authorization denial, session/OAuth security properties, file lifecycle restrictions, and AI approval enforcement.
- Dependency/secret scanning, configuration review, secure headers, rate limits, backups/restore test, incident runbook, and monitoring are verified before public beta.
- Privacy notice, terms, data-processing agreements, data-subject request workflow, and source licensing receive legal review before public launch.
