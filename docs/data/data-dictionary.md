# Data dictionary and persistence rules

This document names the planned logical tables. It defines ownership and responsibility before SQL migrations are written. Exact column types, indexes, and constraints will be finalised alongside each migration.

## Shared conventions

| Convention | Rule |
| --- | --- |
| Identifier | UUIDv7 primary keys. External provider IDs are never primary keys. |
| Ownership | Every private table has `workspace_id`; a parent reference alone is insufficient for authorization. |
| Auditability | Store `created_at`, `updated_at`, actor/source and idempotency data where it explains a change. |
| Deletion | User-owned data is exportable/deletable. Retain only legally necessary audit/security evidence according to the approved retention policy. |
| Localisation | Catalog display values use translation tables/JSONB; user-authored text retains original language. |
| Volatile facts | Source, source record key/URL, observation time, effective dates, confidence, and review state are mandatory when applicable. |

## `identity`

| Table | Ownership | Purpose |
| --- | --- | --- |
| `workspaces` | root | Personal workspace now; team/coach workspace later. |
| `users` | identity | Account, verified email, locale, status, preferences. |
| `memberships` | workspace | User-to-workspace role and lifecycle. |
| `auth_identities` | user | Email/password credentials and Google identities; provider subject is unique per provider. |
| `sessions` | user | Revocable authenticated session/device metadata. |
| `consents` | workspace/user | Versioned purpose, legal basis/notice version, status, timestamp, and withdrawal. |
| `audit_events` | workspace nullable | Security and consequential-action history; append-only. |

## `profile`

| Table | Purpose |
| --- | --- |
| `career_profiles` | Workspace master professional profile and version pointer. |
| `profile_versions` | Immutable profile snapshot used by CVs, recommendations, and reporting. |
| `experiences` | Employer, role, location, dates, employment type, summary. |
| `achievement_bullets` | Reusable, quantified, tagged evidence items. |
| `education_records` | Degree/field/institution/recognition evidence. |
| `projects` | Personal, academic, and professional projects. |
| `languages` | Language and CEFR/proficiency evidence. |
| `skill_evidence` | Skill, proficiency, evidence type, provenance, last used. |
| `certifications` | User-earned credentials, issue/expiry/renewal evidence. |
| `cv_templates` | Controlled rendering templates and version. |
| `cv_snapshots` | Immutable JSON Resume-compatible structured snapshot and input versions. |
| `document_assets` | Object-store asset metadata, checksum, scan/lifecycle status. |
| `public_profiles` | Separately opt-in public visibility, slug, discovery state, publication version. |

## `targeting`

| Table | Purpose |
| --- | --- |
| `campaigns` | A coherent search period, goal, cadence, status, and horizon. |
| `target_roles` | Role/occupation, seniority, compensation bounds, priority. |
| `target_markets` | Country, work arrangement, residency/work-authorisation target, priority. |
| `target_companies` | Desired company, tier, rationale, relationship status. |
| `target_skills` | User-declared focus skills and desired depth. |
| `salary_preferences` | Currency, minimum/target, gross/net basis, location context. |

## `catalog`

| Table | Purpose |
| --- | --- |
| `sources` | Authority, licence, freshness SLA, parser/version, terms review, and state. |
| `occupations` | Canonical occupation taxonomy, external codes, translated labels. |
| `skills` | Canonical skill taxonomy, aliases, translations, relationships. |
| `occupation_skill_requirements` | Skill importance by occupation, market, source, and effective period. |
| `companies` | Canonical employer brand/company record. |
| `legal_entities` | Country-specific legal employer/registry identity. |
| `company_aliases` | Brand, prior, spelling, ATS and source aliases. |
| `sponsorship_evidence` | Dated sponsorship/hiring evidence; never a bare boolean. |
| `visa_routes` | Country-specific route definitions. |
| `visa_route_rules` | Effective-dated, source-backed rules/thresholds. |
| `catalog_credentials` | Certifications and lifecycle state, including retirement. |
| `catalog_courses` | Verified courses, provider, availability, language, URL, and cost metadata. |
| `job_sources` | Public-board/ATS source configuration. |
| `job_postings` | Normalised posting and source/lifecycle evidence. |
| `posting_skills` | Extracted/confirmed skill requirements and confidence. |

## `growth`

| Table | Purpose |
| --- | --- |
| `gap_assessments` | Versioned calculation bound to campaign, profile, inputs, and algorithm/rule version. |
| `gap_items` | Skill/language/credential/experience gap with rank and explainable score. |
| `learning_plans` | User-owned plan derived from an assessment or created manually. |
| `learning_plan_items` | Course/certification/project/practice action, due date, evidence, progress. |
| `visa_readiness_assessments` | Informational assessment against a specific effective-dated visa route rule. |
| `visa_readiness_items` | Evidence/document/readiness action, state, expiry, and source. |

## `pipeline`

| Table | Purpose |
| --- | --- |
| `applications` | Private application aggregate tied to campaign and optional job posting. |
| `application_events` | Append-only status/history events with canonical stage mapping. |
| `application_materials` | CV, cover letter, portfolio, or questionnaire snapshot used at a submission/stage. |
| `application_tasks` | Follow-up, preparation, deadline, and reminder action. |
| `interviews` | Scheduled/completed interview loop/stage. |
| `interview_questions` | Question, response, feedback, preparation source, and outcome. |
| `story_bank_items` | Reusable STAR evidence attached to profile experience and skills. |
| `offers` | Compensation, benefits, equity, visa/relocation support, and offer status. |
| `offer_components` | Componentised/currency-aware values and valuation assumptions. |

## `network`

| Table | Purpose |
| --- | --- |
| `contacts` | Private recruiter, hiring manager, peer, referrer, or coach record. |
| `contact_identities` | Email/profile/social identifier, normalised and source-tagged. |
| `interactions` | Call/email/message/event note linked to contacts and optional application. |
| `referrals` | Request, referrer, application, status, and outcome. |
| `follow_up_rules` | User-defined cadence/conditions, never automatic external send permission. |

## `insights`

| Table | Purpose |
| --- | --- |
| `metric_definitions` | Versioned definitions for a personal/aggregate metric. |
| `workspace_metric_snapshots` | Time-series personal metrics and calculation input version. |
| `benchmark_contributions` | Opted-in, de-identified, purpose-limited contribution eligibility. |
| `published_benchmarks` | Aggregates released only after threshold and privacy checks. |

## `ai` and `platform`

| Table | Purpose |
| --- | --- |
| `ai_conversations` | Workspace/user-scoped conversation metadata and retention state. |
| `ai_proposals` | Structured proposal, sources, input versions, risk class, expiry, status. |
| `ai_approvals` | Approve/reject/edit execution decision and actor. |
| `ai_runs` | Model/provider configuration, cost/usage, outcome, redacted trace pointers. |
| `tool_policies` | Versioned action-type policy and approval requirement. |
| `jobs` | Durable queue, attempts, lock, schedule, payload reference, state. |
| `idempotency_keys` | Request and external-effect deduplication. |
| `integration_connections` | Encrypted credential reference, scope, consent, status, disconnect state. |
| `webhook_deliveries` | Provider event receipt, verification, idempotency, processing state. |
