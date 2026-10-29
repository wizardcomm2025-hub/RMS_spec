# NN — <Verb phrase>

<!-- Rename this file to NN-<use-case-slug>.md, where NN is a stable two-digit number. Numbers never change once set; reordering happens via the README index. See `../../conventions.md` for the full rules. -->

## Summary

<!-- TODO: one paragraph (3–5 sentences). What does the actor experience? What does the system do? Why does it matter? -->

## Prerequisites

<!-- TODO: what must be in place before this use case can be implemented end-to-end. State `none` explicitly if there are no prerequisites. Group by type when it helps (Backend, Data, Depends on, Infrastructure). Link, don't restate. -->

- **Backend.** <endpoint name> live (see [`api-contract.md`](./api-contract.md)).
- **Data.** <table name> created or altered (see [`data-model.md`](./data-model.md)).
- **Depends on.** [`<NN-sibling-use-case>.md`](./<NN-sibling-use-case>.md) — <note on whether the two can be built in parallel against a shared contract or strictly serially>.

## Actors & preconditions

<!-- TODO: who/what triggers this, and what state must hold for it to apply. Actor, trigger, preconditions (data state, auth, feature flags). Errors go in Alternative flows, not here. -->

## Main flow

<!-- TODO: the happy path as a numbered list of system behaviour. 5–10 top-level steps. Each step says what happens, not how it's implemented. Present tense, active voice. -->

1. <step>
2. <step>
3. <step>

## Alternative flows

<!-- TODO: edge cases, errors, branches off the main flow. Each bullet starts with a bold short name, then a description. Cover failures, idempotency, concurrency, race conditions. -->

- **<Short name>.** <description>
- **<Short name>.** <description>

## Acceptance criteria

<!-- TODO: 5–15 numbered, independently testable, observable-from-outside criteria. Gherkin-compatible (Given X, When Y, Then Z) is fine but not required. Cover the main flow at minimum. -->

1. <criterion>
2. <criterion>
3. <criterion>

## Platform notes

> All five sub-sections, in this order. If a part has nothing distinctive to say or isn't touched, write "No platform-specific behaviour beyond the main flow." or "Not touched." rather than omitting it. See `../../conventions.md`, "Platform notes".

### RMS API

<!-- TODO: endpoints (`POST /{Controller}/{Method}`), auth attribute ([Authorize] / [AuthorizeExpired] / [SuperAdminAuthorize] / none), server-side permission checks, CompanyId and UserId taken from the token, messages returned. Link api-contract.md. No code. -->

### Data — Tenant DB

<!-- TODO: tables and stored procedures read or written, the CompanyId filter, IsActive / IsDeleted handling. Link data-model.md. -->

### Data — Central DB

<!-- TODO: control-plane tables or procedures touched (companies, site registry, plans, menu access), or "Not touched." -->

### Integrations

<!-- TODO: Razorpay, PayU, Tally, OCR or other external systems, or "Not touched." -->

### Admin dashboard

<!-- TODO: route, permissions that show or hide actions, states and toasts for this use case. Layout lives in ui-ux.md; link it. For a system-only use case: "No screen." -->

## Configuration

<!-- TODO: what's data-driven vs. hardcoded, and where the data lives. Specs describe *what is configurable*, not the values. -->

## Out of scope

<!-- TODO: explicit exclusions. Each line either links to where the topic is covered or marks it TBD/future. -->

- <topic> — <link or TBD>

## Open questions

<!-- TODO: undecided things. When resolved, append the answer in italics with a date. Do not delete resolved questions. Format: `- Question text? _Resolved YYYY-MM-DD: answer text._` -->

- <question>?
