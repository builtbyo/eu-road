# Business Requirements Document

**Product:** EU Road

**Status:** planning baseline

**Last updated:** 2026-09-12

**Audience:** product, design, engineering, data, and operations

## 1. Product statement

EU Road is a career operating system for people pursuing international work. It connects the full path from defining a career direction to accepting an offer: career profile, market targets, skills development, sponsor evidence, job discovery, applications, relationships, interviews, offers, work authorization, and relocation.

It starts as a personally used product for a Turkish citizen who may require sponsorship. Its architecture and privacy model must support public signup without a re-platforming project.

## 2. The problem

An internationally mobile applicant manages a fragmented process across job boards, spreadsheets, emails, calendar events, CV copies, course catalogs, visa websites, and informal recruiter notes. Generic application trackers omit the information that determines success: target-market fit, proof that an employer can sponsor, tailored career material, response conversion, and time-sensitive work-authorization preparation.

The product must help users make better choices and execute them consistently. It must not pretend to guarantee an offer, sponsorship, visa eligibility, recruiter interest, or legal outcome.

## 3. Goals and measurable outcomes

| Goal | Initial measure |
| --- | --- |
| Make a job search actionable | A user completes a profile, at least one target, and a weekly action plan. |
| Improve application quality | Each application links to a target, role, source, CV version, and next action. |
| Improve learning prioritization | Gap ranking uses target role requirements and posting demand, with evidence. |
| Reduce sponsor uncertainty | Sponsor status is represented as dated evidence, confidence, and source—not a binary claim. |
| Make effort measurable | Funnel conversion, cycle time, follow-up hygiene, and CV performance are visible to the owner. |
| Earn public-product readiness | A second workspace cannot read, reference, or infer private data from another workspace. |

## 4. Users and jobs to be done

### Primary user: international professional

They want to land a role outside their current country or a remote role compatible with their location. They need a credible professional narrative, a measurable search process, reliable work-authorization information, and focused actions.

### Future user: career coach or partner

They want permissioned access to a person's workspace to review strategy and progress without taking ownership of private contacts or applications.

### Future user: public applicant

They need a safe self-service onboarding journey, localised user experience, clear consent, and a path from empty profile to useful recommendations.

## 5. Supported markets and languages

Initial priority markets are the EU, Germany, Netherlands, the United Kingdom, Ireland, and the United States. The country model must support all EU and EEA countries plus future markets.

The launch experience supports English, Turkish, German, Spanish, and French. Product data must store a canonical language-neutral value plus translations where possible. Free-form user content remains in its original language and can be translated only with informed user action.

## 6. Product capabilities

### A. Identity, workspaces, and onboarding

- Email/password and Google sign-in.
- A personal workspace on sign-up; extensible memberships and roles for future coach or partner access.
- Onboarding captures residence, citizenship, work authorization, target destinations, seniority, role interests, languages, salary expectations, and availability.
- Privacy settings control AI use, integrations, aggregate benchmarking, and public-profile visibility independently.

### B. Master career profile and CV library

- Experiences, education, projects, skills, languages, certifications, achievements, links, and work authorization are maintained once and reused.
- Achievement bullets are structured, tagged with skills, metrics, context, and audience; CV versions select and order these reusable facts.
- Every submitted CV and cover letter is frozen as an immutable snapshot with its source profile version, template version, rendered file reference, and content hash.
- A public profile is opt-in and separately controlled from the private workspace.

### C. Targets and career roadmap

- A campaign represents one job-search effort. It has target roles, countries, work arrangements, salary constraints, employer tiers, and a completion horizon.
- A market-fit score explains its components: skill coverage, language readiness, seniority evidence, work-authorization feasibility, and sponsor evidence.
- Gap analysis maps target requirements to current evidence and produces a prioritised plan. Recommendations must point to verified catalog entries and state why they apply.
- Certifications have issuer, validity, renewal, retirement, and effective dates. Retired credentials are never recommended for a new plan.

### D. Sponsor-aware job discovery

- Companies are modelled separately from their legal entities; legal entities can have different countries, hiring records, and sponsorship evidence.
- Evidence includes source, observation date, validity window, visa route, evidence type, confidence, and reviewer status.
- Job postings preserve their source, capture time, normalised role/location/skills, work-authorization language, and lifecycle state.
- Job radar begins with supported public ATS sources and user-saved roles. It remains read-only until source terms, rate limits, and data quality are approved.

### E. Application pipeline, recruiter CRM, and calendar

- Applications belong to a campaign and a workspace, and reference a job posting or a manually entered opportunity.
- Canonical pipeline stages are derived from append-only events. This preserves timestamps for conversion, response time, ghosting, and attribution.
- Contacts, interactions, referrals, follow-up tasks, interview sessions, and notes are private to the workspace.
- Manual entry is always available. Browser extension, email capture, calendar sync, and automated reminders are staged integrations; none may send external messages without approval.

### F. Interview, offer, visa, and relocation desk

- A story bank ties STAR examples to profile evidence, skills, and target roles.
- Interview preparation captures interviewer, stage, questions, feedback, next action, and company-specific research.
- Offer comparison supports compensation components, currencies, equity, benefits, work arrangement, visa support, relocation support, and decision notes.
- Visa readiness tracks documents and evidence against an effective-dated route rule. It is informational tooling, not legal advice.

### G. Insights and shared intelligence

- Personal insight surfaces target coverage, task completion, funnel conversion, response-time distribution, interview-to-offer conversion, and CV variant performance.
- Shared company aggregates require explicit opt-in, strict de-identification, and at least five contributors before publication.
- User-written sponsor, interview, or employer reports are not public until a moderation policy, abuse controls, and minimum viable volume are approved.

### H. AI copilot

- The copilot reads only data the requesting user may access and uses the same service authorization as the product UI.
- It drafts plans, summaries, tailored material, and structured proposals. Mutable actions go through an auditable approval queue; outgoing communication always requires approval.
- The copilot cites the user data and catalog evidence behind material recommendations, exposes uncertainty, and avoids legal, employment, or immigration guarantees.

## 7. Functional requirements

| ID | Requirement |
| --- | --- |
| FR-01 | The system shall isolate every private record by workspace and enforce that isolation server-side. |
| FR-02 | The system shall retain application history as immutable events and derive current status from the latest valid event. |
| FR-03 | The system shall preserve the exact profile/CV artefact used for each submission. |
| FR-04 | The system shall represent sponsor knowledge as evidence with source, time window, confidence, and country/route context. |
| FR-05 | The system shall support manual operation without any third-party integration. |
| FR-06 | The system shall localise interface strings for English, Turkish, German, Spanish, and French. |
| FR-07 | The system shall require explicit consent before processing mailbox/calendar data, publicising a profile, or using data in aggregates. |
| FR-08 | The system shall support export and deletion requests for user-owned data. |
| FR-09 | The system shall record meaningful AI proposals, approvals, executions, failures, and reversals. |
| FR-10 | The system shall display source and effective-date context for volatile catalog/rule information. |

## 8. Non-functional requirements

- **Privacy:** host production data in an EU region initially; minimise personal data; encrypt in transit and at rest; partition secrets from application data.
- **Security:** use secure session management, OAuth PKCE/state validation, server-side authorization, audit logs for sensitive operations, rate limits, and content/file scanning before accepting uploads.
- **Reliability:** migration-based schema, idempotent background jobs, retries with backoff, dead-letter visibility, backups, and restore rehearsal before public beta.
- **Accessibility:** target WCAG 2.2 AA for core workflow; do not make colour the only stage/status indicator.
- **Performance:** the manual core should feel immediate; background ingestion and AI jobs must show durable progress rather than hold a web request open.
- **Observability:** structured logs without CV/notes/email bodies, metrics for product funnel and job health, traceable correlation IDs, and alerting for integration failure.

## 9. Explicit exclusions for the first production release

- Automated application submission, autonomous outreach, or automatic profile publishing.
- Legal representation, visa application filing, or a claim that a company will sponsor.
- Public employer-review/community features before moderation and trust controls exist.
- Separate microservices, a separate NoSQL database, or a public benchmark derived from fewer than five opted-in contributors.

## 10. Open product decisions

1. Select the initial cloud provider and exact EU region before infrastructure work.
2. Decide whether public signup begins in closed beta or open beta, and define anti-abuse controls.
3. Set a default retention period for disconnected email/calendar integration data.
4. Approve initial salary/currency normalisation sources and source licensing.
5. Define public profile discovery rules and whether profile links can be indexed by search engines.
