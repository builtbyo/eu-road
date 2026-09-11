# ADR 0004: Sponsorship knowledge is dated evidence

**Status:** accepted

**Date:** 2026-09-12

## Context

Sponsorship is not a universal company attribute. It varies by legal entity, country, visa route, role, timing, and evidence quality. Public registers and job descriptions can be incomplete or stale.

## Decision

Represent sponsor knowledge in `catalog.sponsorship_evidence`, linked to legal entity, country, and optionally visa route. Every record carries source, observed/effective dates, evidence type, confidence, provenance, reviewer status, and expiration/recheck policy.

## Consequences

The product can explain uncertainty and refresh stale data. It may not present a binary “will sponsor” guarantee.
