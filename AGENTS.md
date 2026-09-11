# EU Road agent instructions

## Product context

EU Road is a privacy-first career operating system for internationally mobile professionals. Its initial focus is Turkish citizens seeking sponsored, relocation-supported, or remote roles in Europe, the UK, Ireland, the Netherlands, Germany, and the United States.

## Non-negotiable engineering rules

- Never work directly on `master`; use a narrow, descriptive branch.
- Use the configured Git user identity only. Never add AI, Claude, bot, or co-author attribution to commits, pull requests, or releases.
- Preserve user data, migrations, and existing work. Never use destructive Git commands without explicit user approval.
- Keep application data in PostgreSQL unless the ARD or an ADR approves an exception. Uploaded document bytes belong in object storage; their metadata belongs in PostgreSQL.
- Treat work authorization, immigration, salaries, sponsor status, and certification lifecycles as dated, source-backed information. Never hard-code volatile rules in application logic.
- User data is workspace-scoped. Enforce ownership in queries, service authorization, and tests; a client-side filter is never sufficient.
- AI can prepare suggestions but must never send messages, submit applications, publish a profile, or change sensitive data without the product's explicit approval model.

## Technology direction

- Backend: Rust, Actix Web, SQLx, PostgreSQL, PostgreSQL full-text search, `pgvector`, and a PostgreSQL-backed job queue.
- Frontend: SvelteKit and TypeScript.
- Prefer a modular monolith and clear domain boundaries over microservices.
- Use UTC timestamps, UUIDv7 identifiers where supported, ISO country/language/currency codes, and structured error responses.

## Required quality practices

- Read the relevant BRD, ARD, ADRs, ERD, and data dictionary before changing a domain.
- Add or update migrations, authorization tests, and documentation with domain changes.
- Keep API contracts backward-compatible or version a breaking change deliberately.
- Run the smallest meaningful formatter, linter, and test set before proposing a change.

## Documentation and skills

- Keep this file short and repository-wide. Put specialized rules close to the relevant code only when they become necessary.
- Create a project skill only for a repeated, stable workflow with concrete inputs, outputs, checks, and references. Do not create skills merely to restate this file.
