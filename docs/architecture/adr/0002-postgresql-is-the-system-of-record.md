# ADR 0002: PostgreSQL is the system of record

**Status:** accepted

**Date:** 2026-09-12

## Context

Core product questions cross profile, targeting, applications, contacts, and outcomes. Separate NoSQL stores would complicate ownership, deletion, reporting, and consistency without a demonstrated need.

## Decision

Use PostgreSQL for relational facts, JSONB snapshots, full-text search metadata, vector embeddings, auditing, and durable job coordination. Use EU object storage only for binary file bytes, with PostgreSQL asset metadata as the authority.

## Consequences

Normalise query-critical facts and use JSONB only where shape legitimately varies. A future datastore needs a written data-ownership boundary, migration plan, privacy/deletion plan, and operational evidence.
