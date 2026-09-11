# MVP scope and delivery roadmap

## Delivery principle

EU Road will reach breadth through coherent vertical slices, not by shipping disconnected dashboards. Every increment must be usable by the dogfooding user, leave an auditable data trail, and preserve public-product constraints.

## Release sequence

| Increment | Outcome | Included | Exit gate |
| --- | --- | --- | --- |
| 0. Product foundation | A stable shared plan | BRD, ARD, ADRs, ERD, API conventions, threat model, Git workflow, agent instructions | Documentation review complete; unresolved decisions recorded. |
| 1. Secure manual core | A user can run a complete manual job search | Auth, workspace, onboarding, profile, targets, companies, applications, pipeline events, contacts, tasks, basic dashboard | Second-workspace isolation tests pass; a real application can be recorded end-to-end. |
| 2. Career intelligence | A user gets evidence-based improvement priorities | Skill taxonomy, catalog baseline, profile-to-target gaps, learning plan, certifications, fit explanation | Recommendations cite requirements and catalog entries; no retired certification is suggested. |
| 3. Sponsor-aware radar | A user can prioritise credible opportunities | Legal entities, sponsor evidence, visa route rules, job-posting ingestion, job-save workflow | Evidence shows source, date, confidence, and country/route; ingestion is observable and idempotent. |
| 4. Career material and interview desk | A user can tailor and learn from applications | CV snapshots, template rendering, ATS comparison, stories, interview loops, offer comparison | Submitted artefacts are immutable; pipeline conversion attributes to a version. |
| 5. Integrations and copilot | Assisted execution with consent | Browser extension, email/calendar imports, reminders, proposal queue, copilot, automation preferences | All writes are approval-aware; outgoing communication remains user-approved. |
| 6. Public beta | A trustworthy multi-user product | Sign-up controls, export/deletion, localisation, operational runbooks, auditability, support flows | Security, privacy, accessibility, backups, and incident readiness are verified. |

## What “everything” means

All requested product areas are in scope. They are deliberately sequenced so that automation and AI improve a reliable process rather than create an opaque one.

```text
Profile + Targets
        ↓
Growth plan + Sponsor-aware opportunities
        ↓
Tailored applications + Recruiter relationship management
        ↓
Interviews + Offers + Visa readiness
        ↓
Insights feed back into goals, strategy, and material
```

## MVP acceptance scenarios

### Scenario A: New applicant

1. Sign in with email/password or Google.
2. Choose English, Turkish, German, Spanish, or French.
3. Create a profile, a search campaign, and target roles/countries.
4. Log an application and the CV snapshot used.
5. Add a recruiter contact, interaction, and follow-up.
6. Record a response event and see the pipeline/dashboard update.

### Scenario B: Sponsor-focused search

1. Create a target for a role/country/work-authorization route.
2. Inspect company/legal-entity sponsor evidence with source and date.
3. Save a job and connect it to the target.
4. View which profile gaps, language requirements, or sponsor uncertainty affect fit.

### Scenario C: AI-assisted but user-controlled action

1. Ask the copilot to propose follow-ups and CV improvements.
2. Review sources and proposed changes.
3. Approve, reject, or edit a proposal.
4. Confirm that nothing external was sent and every accepted mutation is auditable.

## Release controls

- Each increment is released behind a feature flag until it has dogfooding evidence.
- New country/visa integrations require a source review, licensing check, data freshness policy, and failure behaviour.
- New languages require translated navigation, validation errors, empty states, dates/numbers/currencies, and accessibility verification—not merely machine-translated headings.
- The roadmap does not imply a delivery date. Estimates follow the first implementation backlog and technical spikes.
