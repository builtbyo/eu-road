# EU Road

EU Road is a career operating system for internationally mobile professionals. It helps a person move from career goals to a signed offer through a connected workflow for profile building, targeting, sponsorship research, applications, outreach, interviews, growth, and visa readiness.

The first use case is a Turkish citizen seeking sponsored or relocation-supported roles in the EU, UK, Ireland, Netherlands, Germany, and the United States. The product is designed for public signup from its first architecture decisions.

## Status

The repository is in product-foundation planning. No application code exists yet. The current documentation defines the agreed product direction and the decisions required before implementation begins.

## Documentation

- [Business requirements](docs/product/BRD.md)
- [MVP scope and delivery roadmap](docs/product/mvp-scope-and-roadmap.md)
- [Architecture record](docs/architecture/ARD.md)
- [Architecture decisions](docs/architecture/adr/)
- [Data model and ERD](docs/data/ERD.md)
- [Data dictionary](docs/data/data-dictionary.md)
- [API contract](docs/api/api-contract.md)
- [AI copilot guardrails](docs/ai/copilot-guardrails-and-evals.md)
- [Privacy and threat model](docs/security/privacy-threat-model.md)
- [Engineering and Git workflow](docs/engineering/git-workflow.md)

## Working agreements

- `master` is protected in practice: all work starts on a scoped branch and reaches `master` only through a reviewed pull request.
- Commits use the repository owner's configured Git identity. Do not add AI, Claude, or automated co-author trailers.
- Product, architecture, and data changes must update the corresponding documentation in the same branch.
- The application will use Rust with Actix Web, SvelteKit, and PostgreSQL. See the ARD before adding infrastructure or services.

## Repository structure (planned)

```text
apps/
  web/                 # SvelteKit application
services/
  api/                 # Actix Web modular monolith
  worker/              # asynchronous jobs, same Rust workspace
crates/                # shared Rust libraries and domain modules
db/
  migrations/
  seeds/
docs/
```
