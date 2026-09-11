# AI copilot guardrails and evaluation plan

## Purpose

The EU Road copilot helps users understand and execute their job-search strategy. It can explain profile gaps, prioritise work, draft career material, prepare interview plans, summarise private data, and propose structured changes. It is never the decision maker for hiring, immigration, or user-controlled external communication.

## Authority model

The copilot runs as the requesting authenticated user inside one authorised workspace. It receives only the minimum fields required for the active task. The same backend service methods and authorization checks serve both the UI and AI tools.

| Action | Policy in first release |
| --- | --- |
| Read authorised profile, targets, applications, contacts | Allowed when required for the request; access is audited. |
| Produce explanation, plan, draft, or analysis | Allowed; response must distinguish facts, inference, uncertainty, and user-provided content. |
| Create draft tasks, learning-plan items, or profile changes | Create a proposal; user approval required. |
| Run a reversible low-risk change | Proposal by default; future auto-execution needs explicit policy, undo, and evaluation evidence. |
| Send email/message, submit application, publish profile | Always user-approved. The copilot cannot bypass the approval surface. |
| Modify consents, sharing, integration credentials, export/delete data | Always user-approved and security-audited. |
| State that a company will sponsor or a user will receive a visa/offer | Prohibited. Explain evidence and uncertainty instead. |

## Proposal lifecycle

```text
drafted → awaiting_approval → approved → executing → executed
                              ↘ rejected
                              ↘ expired
                              ↘ failed
```

An `ai_proposal` stores:

- action type and schema version;
- workspace/user context and policy version;
- input snapshot references, catalog/rule/source references, and model configuration;
- a human-readable explanation, structured diff/payload, risk class, expiry, and rollback reference if applicable;
- decision/execution audit events and failure information.

The domain command validates the proposal again at execution time. Approval never trusts an old or altered payload silently.

## Grounding requirements

- Personal claims cite source records by type and timestamp, such as experience, achievement, application event, or interaction.
- Catalog claims cite source, observation/effective date, confidence, and freshness state.
- A recommendation explains its scoring inputs: for example, target-role requirement, current evidence, and job-posting demand.
- When evidence is missing or conflicting, the response says so and offers a verification action rather than inventing certainty.
- Advice on visas or employment law is informational and points to the dated source; the product does not provide legal advice.

## Privacy and prompt boundaries

- Do not send CVs, full notes, emails, contact information, or integration raw payloads to a model unless the active request needs them and the user has consented to the configured processing.
- Redact provider secrets, session tokens, cookies, credentials, and unnecessary direct identifiers before model calls and observability capture.
- Keep raw prompts/responses only under an approved retention policy. Store reference pointers and redacted structured traces when sufficient for audit/evaluation.
- Never use a person's private content to train shared functionality or populate public intelligence without separate, explicit consent.

## Evaluation before release

Each supported copilot action needs a curated, de-identified evaluation set and acceptance thresholds.

| Capability | Primary checks |
| --- | --- |
| Gap analysis | Correctly identifies evidence and gaps; does not recommend retired credentials; explains ranking. |
| CV tailoring | Preserves factual accuracy; does not fabricate metrics/employment; respects the supplied job posting. |
| Sponsor/visa summary | Names source and date; represents uncertainty; avoids guarantees and legal conclusions. |
| Pipeline summary | Calculates stage/status from events correctly; does not expose another workspace. |
| Proposal generation | Valid action schema; correct approval class; no direct side effect before approval. |
| Multilingual use | Meaning remains consistent across English, Turkish, German, Spanish, and French; user content is not translated/published without action. |

Release gates include authorization tests, prompt-injection/adversarial data tests, groundedness review, factuality review by a domain owner, and manual review of high-risk output classes.

## Failure behaviour

If a source is stale, a tool fails, a model response is malformed, or policy evaluation is unavailable, the copilot must not infer success. It reports the failure, leaves no partial external action, preserves enough audit context for diagnosis, and offers a safe retry or manual alternative.
