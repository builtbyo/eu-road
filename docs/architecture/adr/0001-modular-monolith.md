# ADR 0001: Start with a modular monolith

**Status:** accepted

**Date:** 2026-09-12

## Context

The product has many related domains, but no traffic or team-scale evidence that warrants distributed services. Private workspace ownership, pipeline event consistency, and auditability benefit from transactional coordination.

## Decision

Use one Rust workspace and deployable API/worker system organised by domain modules. A module owns its command handlers, persistence repository, authorization rules, jobs, and tests. Module-to-module behaviour is reached through internal interfaces, never ad-hoc table access.

## Consequences

We minimise operations and distributed consistency overhead while retaining extractable boundaries. A future service extraction needs an ADR, measured justification, an API/event contract, ownership migration, and observability plan.
