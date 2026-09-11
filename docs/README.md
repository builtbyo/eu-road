# Documentation map

This directory is the source of truth for product and technical decisions until implementation creates more precise code-level documentation.

| Area | Source | Purpose |
| --- | --- | --- |
| Product | `product/BRD.md` | Problem, personas, requirements, boundaries, and outcomes. |
| Delivery | `product/mvp-scope-and-roadmap.md` | Increments, milestones, release gates, and deferred work. |
| Architecture | `architecture/ARD.md` | System shape, boundaries, technology choices, and operational posture. |
| Decisions | `architecture/adr/` | Immutable records for decisions that are expensive to reverse. |
| Data | `data/ERD.md`, `data/data-dictionary.md` | Logical model, ownership, relationships, and persistence rules. |
| Interfaces | `api/api-contract.md` | API conventions and the first resource contract. |
| AI | `ai/copilot-guardrails-and-evals.md` | Copilot scope, approvals, evidence, safety, and evaluation. |
| Privacy | `security/privacy-threat-model.md` | Data classification, access rules, retention, and threats. |
| Engineering | `engineering/git-workflow.md` | Branch, review, commit, and quality rules. |

## Change protocol

1. Record a product or architecture decision before implementation when it affects data ownership, security, external integrations, or release scope.
2. Amend the BRD for requirements, the ERD/data dictionary for persisted information, and the API contract for public behavior.
3. Add a numbered ADR for decisions with reasonable alternatives or future migration cost.
4. Link delivery work to a roadmap increment and its acceptance criteria.
