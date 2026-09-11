# Git workflow and delivery rules

## Branch policy

`master` is the stable integration branch. No development work, direct commit, or force push occurs on `master`.

Create one branch per reviewable goal:

```text
docs/product-foundation
feat/manual-application-pipeline
fix/workspace-authorization-check
chore/upgrade-sqlx
```

Start a branch from current `master`; keep it focused. If a branch becomes too broad, split the work into dependent pull requests rather than hiding unrelated changes in one review.

## Commit policy

- Use the repository owner's configured Git name/email.
- Do not add `Co-authored-by`, AI, Claude, bot, model, or assistant attribution to commit messages, PR descriptions, release notes, or Git metadata.
- Prefer clear, conventional-style subjects: `docs: establish product foundation`, `feat(pipeline): record immutable application events`, `fix(auth): scope session lookup to workspace`.
- Separate unrelated documentation, schema, feature, test, and refactor changes where that makes review safer.
- Never commit passwords, OAuth tokens, API keys, CVs, personal applications, production data, generated secrets, or local environment files.

## Pull request policy

Each PR must state:

1. the user-facing outcome and scope;
2. linked BRD/ADR/data/API changes where applicable;
3. migration, privacy, integration, or localisation impact;
4. checks run and result;
5. rollout/rollback behaviour for meaningful changes.

Merge only after review and required checks. Rebase/merge strategy can be selected later, but it must preserve the clear human-authored commit identity and never rewrite `master` history.

## Required review questions

- Does every private query enforce workspace ownership server-side?
- Does the schema preserve data history and deletion/export requirements?
- Are new country, sponsor, visa, course, or salary facts source-backed, dated, and appropriately licensed?
- Does a new AI action respect proposal/approval policy and retain evidence?
- Is user-generated or integration content treated as untrusted input?
- Does the change work in all supported locales and remain accessible?
- Are docs, tests, migrations, and operational behaviour updated together?

## Quality gates by change type

| Change | Minimum checks |
| --- | --- |
| Rust API/domain | formatter, linter, targeted unit/integration tests, authorization tests. |
| Database migration | migration up/down strategy, constraints/index review, seed/test coverage, data-backfill plan if needed. |
| SvelteKit UI | type check, unit/component tests, accessibility check, supported-locale review. |
| Integration | contract tests/mocks, scope/secret review, idempotency/retry test, source policy update. |
| AI | structured-output validation, proposal approval tests, eval regression set, redaction/grounding check. |
| Documentation-only | link check, terminology/data-model consistency review. |

## Documentation ownership

The PR that changes behaviour owns the documentation update. The BRD describes why/what, ADRs record expensive choices, ERD/data dictionary describe stored facts, API contract describes public behaviour, and operational docs explain safe running. Do not postpone essential documentation to an unrelated future task.

## Project skills

Do not add a project `SKILL.md` during initial planning. Add one only when a workflow repeats enough to justify executable, scoped instructions—for example, authoritative visa-source ingestion or database migration review. A skill must name its inputs, output, checks, source of truth, and failure handling; it must not duplicate repository-wide rules from `AGENTS.md`.
