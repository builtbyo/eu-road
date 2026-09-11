# ADR 0005: AI proposes; the user approves consequential actions

**Status:** accepted

**Date:** 2026-09-12

## Context

The copilot must be useful across highly sensitive personal data and external career actions. Autonomous changes risk wrong data, unwanted disclosure, and reputational harm.

## Decision

AI tools use the same user/workspace authorization as the UI. Every mutation becomes a structured proposal with input/source references, diff, policy outcome, expiry, and audit trail. Sending messages, submitting applications, changing sharing/consent, and deleting data always require approval.

## Consequences

The first copilot is intentionally less autonomous but more trustworthy and debuggable. Low-risk reversible actions may later auto-execute only after explicit policy, undo capability, and evaluation evidence.
