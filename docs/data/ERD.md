# Logical ERD

This is a logical data model, not executable DDL. It captures ownership and cardinality; the [data dictionary](data-dictionary.md) supplies the full domain inventory and migration conventions. Tables are split by bounded context so they remain readable.

## Modelling conventions

- Private tables have `workspace_id` and must be authorised by workspace membership.
- IDs are UUIDv7 where available. All timestamps are UTC `timestamptz`.
- Every mutable, user-facing aggregate includes `created_at`, `updated_at`, and an actor/source where relevant; these columns are abbreviated in diagrams.
- Content that was submitted, rendered, or used by a recommendation is immutable/versioned.
- ISO 3166-1 alpha-2 country codes, ISO 639/BCP 47 language tags, ISO 4217 currency codes, and IANA time zones are stored as constrained text/reference data.

## Identity and profile

```mermaid
erDiagram
    WORKSPACES ||--o{ MEMBERSHIPS : has
    USERS ||--o{ MEMBERSHIPS : joins
    USERS ||--o{ AUTH_IDENTITIES : authenticates_with
    WORKSPACES ||--|| CAREER_PROFILES : owns
    CAREER_PROFILES ||--o{ EXPERIENCES : contains
    CAREER_PROFILES ||--o{ ACHIEVEMENT_BULLETS : owns
    EXPERIENCES ||--o{ ACHIEVEMENT_BULLETS : supplies_context
    CAREER_PROFILES ||--o{ SKILL_EVIDENCE : demonstrates
    ACHIEVEMENT_BULLETS ||--o{ BULLET_SKILLS : tags

    WORKSPACES {
        uuid id PK
        string kind
        string displayName
        string defaultLocale
    }
    USERS {
        uuid id PK
        string email UK
        string status
        string preferredLocale
    }
    MEMBERSHIPS {
        uuid id PK
        uuid workspaceId FK
        uuid userId FK
        string role
        string status
    }
    AUTH_IDENTITIES {
        uuid id PK
        uuid userId FK
        string provider
        string providerSubject UK
    }
    CAREER_PROFILES {
        uuid id PK
        uuid workspaceId FK
        string headline
        string residenceCountry
        string profileVersion
    }
    EXPERIENCES {
        uuid id PK
        uuid profileId FK
        string employerName
        string title
        date startedOn
        date endedOn
    }
    ACHIEVEMENT_BULLETS {
        uuid id PK
        uuid profileId FK
        uuid experienceId FK
        text body
        jsonb metrics
        string evidenceStrength
    }
    SKILL_EVIDENCE {
        uuid id PK
        uuid profileId FK
        uuid skillId FK
        string proficiency
        string evidenceType
    }
    BULLET_SKILLS {
        uuid bulletId PK, FK
        uuid skillId PK, FK
    }
```

## Targets, applications, and private network

```mermaid
erDiagram
    WORKSPACES ||--o{ CAMPAIGNS : owns
    CAMPAIGNS ||--o{ TARGET_ROLES : defines
    CAMPAIGNS ||--o{ TARGET_MARKETS : defines
    CAMPAIGNS ||--o{ APPLICATIONS : contains
    APPLICATIONS ||--o{ APPLICATION_EVENTS : records
    APPLICATIONS ||--o{ APPLICATION_MATERIALS : uses
    WORKSPACES ||--o{ CONTACTS : owns
    CONTACTS ||--o{ INTERACTIONS : participates_in
    APPLICATIONS ||--o{ INTERACTIONS : relates_to
    APPLICATIONS ||--o{ INTERVIEWS : contains
    APPLICATIONS ||--o{ OFFERS : may_receive

    CAMPAIGNS {
        uuid id PK
        uuid workspaceId FK
        string name
        string status
        date targetEndDate
    }
    TARGET_ROLES {
        uuid id PK
        uuid campaignId FK
        uuid occupationId FK
        string seniority
        decimal salaryFloor
    }
    TARGET_MARKETS {
        uuid id PK
        uuid campaignId FK
        string countryCode
        string workArrangement
        uuid visaRouteId FK
    }
    APPLICATIONS {
        uuid id PK
        uuid workspaceId FK
        uuid campaignId FK
        uuid jobPostingId FK
        string sourceType
        datetime appliedAt
    }
    APPLICATION_EVENTS {
        uuid id PK
        uuid applicationId FK
        string eventType
        string canonicalStage
        datetime occurredAt
        string actorType
    }
    APPLICATION_MATERIALS {
        uuid id PK
        uuid applicationId FK
        uuid cvSnapshotId FK
        uuid documentAssetId FK
        string purpose
    }
    CONTACTS {
        uuid id PK
        uuid workspaceId FK
        string fullName
        string role
        string employerName
    }
    INTERACTIONS {
        uuid id PK
        uuid workspaceId FK
        uuid contactId FK
        uuid applicationId FK
        string channel
        datetime occurredAt
    }
    INTERVIEWS {
        uuid id PK
        uuid applicationId FK
        string interviewType
        datetime scheduledAt
        string outcome
    }
    OFFERS {
        uuid id PK
        uuid applicationId FK
        string status
        string currencyCode
        decimal baseSalary
    }
```

## Shared catalog, growth, and controlled AI

```mermaid
erDiagram
    COMPANIES ||--o{ LEGAL_ENTITIES : has
    LEGAL_ENTITIES ||--o{ SPONSORSHIP_EVIDENCE : has
    LEGAL_ENTITIES ||--o{ JOB_POSTINGS : employs_for
    VISA_ROUTES ||--o{ VISA_ROUTE_RULES : changes_over_time
    JOB_POSTINGS ||--o{ POSTING_SKILLS : requires
    CAMPAIGNS ||--o{ GAP_ASSESSMENTS : receives
    GAP_ASSESSMENTS ||--o{ GAP_ITEMS : ranks
    GAP_ITEMS }o--o{ CATALOG_CREDENTIALS : addressed_by
    WORKSPACES ||--o{ AI_PROPOSALS : receives
    AI_PROPOSALS ||--o{ AI_APPROVALS : decided_by

    COMPANIES {
        uuid id PK
        string canonicalName UK
        string website
        string industry
    }
    LEGAL_ENTITIES {
        uuid id PK
        uuid companyId FK
        string legalName
        string countryCode
        string registryId
    }
    SPONSORSHIP_EVIDENCE {
        uuid id PK
        uuid legalEntityId FK
        uuid visaRouteId FK
        string evidenceType
        decimal confidence
        date observedOn
    }
    VISA_ROUTES {
        uuid id PK
        string countryCode
        string code UK
        string name
    }
    VISA_ROUTE_RULES {
        uuid id PK
        uuid visaRouteId FK
        date effectiveFrom
        date effectiveTo
        jsonb ruleDefinition
    }
    JOB_POSTINGS {
        uuid id PK
        uuid legalEntityId FK
        string sourceUrl UK
        string title
        string countryCode
        datetime firstSeenAt
    }
    POSTING_SKILLS {
        uuid postingId PK, FK
        uuid skillId PK, FK
        string importance
    }
    GAP_ASSESSMENTS {
        uuid id PK
        uuid campaignId FK
        uuid profileVersionId FK
        datetime computedAt
    }
    GAP_ITEMS {
        uuid id PK
        uuid assessmentId FK
        uuid skillId FK
        decimal priorityScore
        string rationale
    }
    CATALOG_CREDENTIALS {
        uuid id PK
        string provider
        string code
        date retiredOn
    }
    AI_PROPOSALS {
        uuid id PK
        uuid workspaceId FK
        string actionType
        string status
        datetime expiresAt
    }
    AI_APPROVALS {
        uuid id PK
        uuid proposalId FK
        uuid userId FK
        string decision
        datetime decidedAt
    }
```

## Key integrity rules

1. A private child row must reference a parent from the same `workspace_id`; enforce this with composite foreign keys or a trigger/check pattern where PostgreSQL cannot express it directly.
2. Applications must reference a campaign in the same workspace. Contacts and interactions cannot cross workspaces.
3. A CV snapshot used by an application is immutable and retains the profile/template/source inputs that produced it.
4. Application events cannot be updated or deleted through normal product commands. Corrections use a superseding event.
5. Sponsor evidence must include a source and observation date; confidence is a bounded value with documented calculation/meaning.
6. Rules and credentials must have effective dates. Recommendations resolve the version applicable at evaluation time.
7. AI proposals store only structured action payloads and references; they do not bypass the command/authorization layer.
