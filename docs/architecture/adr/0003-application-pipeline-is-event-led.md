# ADR 0003: Represent application progress as immutable events

**Status:** accepted

**Date:** 2026-09-12

## Context

Application history determines funnel conversion, response time, ghosting, follow-ups, and the effectiveness of career material. A mutable status field loses this evidence and is difficult to audit.

## Decision

`pipeline.application_events` is append-only. Each event maps to a canonical stage, has an effective timestamp, actor/source, optional note, and idempotency key. Current stage is a projection from valid events; corrections are represented as superseding events rather than in-place history edits.

## Consequences

Reporting becomes reliable and future status mappings can evolve. Reads may need a maintained projection when usage makes event reconstruction expensive.
