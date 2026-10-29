# Conventions for writing specs

> **What this is:** the style guide and template for **specifications** in this folder. It governs how use-case files, feature READMEs, data models, API contracts, UI/UX files, test cases and ADRs are written.
>
> **What this is not:** a code style guide. Code conventions live in `RMS_API/CLAUDE.md`, `RMS_NextJS/CLAUDE.md` and `RMS_API/specs/UI_UX_STANDARDS.md`.

Read this before writing or significantly changing a spec.

For directory structure and workflow, see [`README.md`](./README.md). This document is about the **content** of specs.

> **Before a spec exists.** Discovery Q&A and client-provided facts are captured under [`intake/`](./intake/). That material is not a spec — it's the raw input that becomes one.

## The use-case template

Every use-case file (`features/<feature>/NN-<use-case>.md`) contains these sections in this order:

```
# NN — <Verb phrase>

## Summary
## Prerequisites
## Actors & preconditions
## Main flow
## Alternative flows
## Acceptance criteria
## Platform notes
### RMS API
### Data — Tenant DB
### Data — Central DB
### Integrations
### Admin dashboard
## Configuration
## Out of scope
## Open questions
```

The worked example is [`features/setting-location/`](./features/setting-location/). Mirror its shape.

### Title

Format: `NN — <verb phrase>` where `NN` is a stable two-digit number.

- Numbers are stable. Don't renumber when reordering — the feature `README.md` index controls display order.
- The verb phrase describes the user or system action ("Add a location", "Switch a location on or off").

### Summary

One paragraph (3–5 sentences): the actor, the action, the outcome, and why it matters. No implementation detail.

### Prerequisites

What must be in place before this use case can be implemented end-to-end. A *gate*: if a prerequisite is unmet, work pauses or proceeds against a stub — not blindly.

- Bullet list; each bullet names the prerequisite and links to where it is defined.
- Group by type when it helps (`Backend`, `Data`, `Depends on`).
- State `none` explicitly when there are none.
- **Link, don't restate.** Endpoints → `api-contract.md`; tables and stored procedures → `data-model.md`; sibling use cases → their file.
- Cross-feature dependencies belong in the feature `README.md`'s `## Depends on` section.

### Actors & preconditions

Who triggers the use case (company user with a given permission, SuperAdmin, system), the trigger, and the state that must hold. Errors go in Alternative flows.

### Main flow

The happy path as a numbered list of system behaviour, 5–10 top-level steps. **What** happens, not how. Present tense, active voice. Reference data and APIs by their names in `data-model.md` / `api-contract.md`.

### Alternative flows

Edge cases, errors and branches. Each bullet starts with a bold short name: `- **Duplicate name.** …`. Cover failures, validation, double submit, concurrency, inactive / deleted state, permissions.

### Acceptance criteria

Numbered, observable, independently testable conditions; "Given X, when Y, then Z" style. 5–15 per use case. Prefer observable outcomes ("the location appears in the list as Inactive") over implementation language. For admin screens, include the responsive and feedback-state checks required by `UI_UX_STANDARDS.md`, or link to the feature's `ui-ux.md` criteria.

### Platform notes

Every use case has the same five sub-sections, in this order:

| Sub-section | Covers |
|---|---|
| **RMS API** | Endpoints (`POST /{Controller}/{Method}`), the auth attribute (`[Authorize]`, `[AuthorizeExpired]`, `[SuperAdminAuthorize]`, none), server-side permission checks, where `CompanyId` / `UserId` come from (always the token), response messages. |
| **Data — Tenant DB** | Tables and stored procedures read or written in the company database, the `CompanyId` filter, `IsActive` / `IsDeleted` handling. |
| **Data — Central DB** | Control-plane tables and procedures (companies, site registry, plans, menu access). Most company features write "Not touched." |
| **Integrations** | Razorpay, PayU, Tally, OCR or other external systems. |
| **Admin dashboard** | Screen behaviour in `RMS_NextJS`: route, permissions that show/hide actions, states, toasts. Detailed layout lives in the feature's `ui-ux.md`; link to it. |

If a part has nothing distinctive to say, write "No platform-specific behaviour beyond the main flow." or "Not touched." rather than omitting it.

### Configuration

What is data-driven vs hard-coded, and where the data lives (company settings, master tables, role permissions, `appsettings`). Describe **what is configurable**, not the values.

### Out of scope

Bullet list. Each line links to where the topic is covered or marks it TBD/future.

### Open questions

- When resolved, append the answer in italics with a date; never delete the question: `- Question? _Resolved YYYY-MM-DD: answer._`
- Cross-cutting questions go in the feature `README.md`.
- Claude Code treats open questions as blockers — do not infer answers; ask.

## File scope and context-window economy

Each use-case file is one **vertical slice**: one user-observable behaviour through the UI, the API call, the service and the data write. An agent should be able to implement and verify that slice from the use-case file plus the feature `README.md`, `data-model.md`, `api-contract.md`, `ui-ux.md` and its test-case files. Shared content lives in those shared files and is linked, not duplicated.

## Writing style

- **Active voice, present tense.**
- **Behaviour, not implementation.** Specs say what; code says how.
- **No values that should be configuration.**
- **Product and table names are allowed** (SQL Server, Razorpay, `dbo.Warehouse`). Specific libraries are implementation detail.
- **One idea per sentence.**

## Cross-references and linking

- Link other specs by relative path.
- **Never duplicate behaviour.** "Out of scope" may briefly mention a topic before linking.
- Reference Figma frames by link AND export a screenshot into `assets/`.

## Other document types

### Feature `README.md`

- One-paragraph summary
- Index table of use cases
- Key concepts (or a link to the glossary in `project-overview.md`)
- Links to `data-model.md`, `api-contract.md`, `ui-ux.md`, `test-cases/README.md`, and the implementation folder `RMS_API/specs/<feature>/`
- The feature code used in test IDs (for example `LOC`)
- **Depends on** — cross-feature dependencies, or `None — this feature has no cross-feature dependencies.`
- **Decisions** and **cross-cutting open questions**
- **Feature-wide out of scope**

### Feature `ui-ux.md`

Required for every feature with an admin-dashboard screen. It is the complete **UI / UX section** demanded by `RMS_API/specs/UI_UX_STANDARDS.md` (its 10-point checklist): page anatomy, columns / fields, dialog, feedback states, confirmations, permissions, responsive behaviour, accessibility, shared components, template impact. List pages follow the §12 recipe and list only what differs. Copy text the user sees appears here verbatim, because tester cases quote it.

### Feature `developer-guide.html`

Required, one per feature, **HTML not markdown**. Copy [`features/template/developer-guide.html`](./features/template/developer-guide.html), keep its styling. Contents: what the feature is; v1 scope (link to the README); how it works end-to-end; a **What matters where you sit** section with one subsection per Platform-notes part. Summarise and link — the formal specs win if they disagree.

### `data-model.md`

One per feature. The data the feature owns in the **Tenant DB** and, if any, the **Central DB**:

- Tables created, altered or relied on, with primary key and purpose
- Columns of new or altered tables, constraints, and the rules checked in stored procedures (always including the `CompanyId` filter)
- Stored procedures added or changed (scripts are `CREATE OR ALTER` / `IF NOT EXISTS`, re-runnable)
- Seed data, indexes that matter, row lifecycle (`IsActive`, `IsDeleted`), migration notes
- **Naming follows the existing schema.** Read the tables you touch and match their style.
- Physical SQL lives in `RMS_API/ProductManagement.API/Database Scripts/` and must match this file.

### `api-contract.md`

One per feature: endpoints, request and response fields, auth attribute, every message the endpoint can return, and which responses come from shared layers (see [API conventions](./project-overview.md#api-conventions)). `api/openapi.yaml` is a placeholder until GETTING-STARTED item 2 is decided; until then `api-contract.md` is the contract.

### Test cases (`test-cases/`)

Every feature has a `test-cases/` subfolder derived from its use cases, data model, API contract and UI/UX. Start from [`features/template/test-cases/`](./features/template/test-cases/).

The goal is a feature that is **fail-safe inside out**: testers cover everything a user can do through the screen; developers cover everything a direct or manipulated request can do.

**Two kinds, one pair of files per use case:**

| File | For | Covers | Contains |
|---|---|---|---|
| `NN-<use-case>.tester.md` | Testers: manual, or UI automation | Normal flows and every edge case a user can reach through the admin dashboard, including permissions (log in as a user without the permission), responsive sizes and feedback states | Precondition, plain steps, the **exact text** on screen and the visible outcome. No endpoints, request bodies, HTTP codes or SQL. |
| `NN-<use-case>.developer.md` | Developers: Postman / Postman CLI, SQL | Input the UI can't produce: no or tampered token, another company's IDs or host, a company user on a SuperAdmin endpoint, extra or forbidden fields, garbage and out-of-range values, double submits, injection text | Caller, the request and what is manipulated, then the expected HTTP status, `status` / `message`, and the **database state** after the call |

**System-only use cases** (no screen) have only a developer file; the coverage table says `Tester: n/a — no UI`.

`test-cases/README.md` holds the shared fixtures, the coverage matrix, the developer endpoint checklist and how to run the developer cases (Postman CLI).

**IDs:**
- `<CODE>-<NN>-T<nn>` for tester cases and `<CODE>-<NN>-D<nn>` for developer cases, for example `LOC-02-T03`, `LOC-02-D05`.
- IDs are stable and never reused. A test that no longer applies stays in the file, marked `Removed YYYY-MM-DD: <reason>`.

**Coverage rules:**
- Every acceptance criterion and alternative flow is covered by at least one test case, tester or developer.
- Every endpoint a use case touches has developer cases for each category below, or the endpoint checklist marks the category `n/a` with a reason. Never silently skipped.

| Category | What it proves |
|---|---|
| Authentication | No token, a malformed token, an expired token, a token signed with another key → rejected; nothing written. |
| Company isolation | (a) IDs of another company's rows, sent with a valid token of company A, are rejected or return nothing; nothing written; no data of the other company leaks. (b) A token of company A sent with company B's `Origin` is rejected by the tenant middleware. (c) An unknown or inactive `Origin` is rejected. |
| SuperAdmin boundary | A company user calling a `/SuperAdmin/*` (or `[SuperAdminAuthorize]`) endpoint is rejected; a SuperAdmin token does not act on company endpoints unless the spec says so. |
| Permission | A company user without the menu permission (for example `Location.Edit`) calls the endpoint directly. Expected result as the spec defines it — see *Known gaps* in `project-overview.md`. |
| Input validation | Missing, empty, whitespace-only, over-long, wrong-type, negative / zero / huge IDs, unknown, deleted or inactive references → rejected with a clear message. |
| Business-rule bypass | Calls the UI never makes (actions it hides or disables, values it never sends) are still refused or neutralised by the server. |
| Over-posting | Extra or forbidden fields (`CompanyId`, `UserId`, `CreatedBy`, `IsDeleted`…) in the body are ignored. Identity and company come from the token. |
| Concurrency / idempotency | Double submits, parallel edits and races leave consistent data and no duplicates. |
| Injection / encoding | SQL, script and Unicode text are stored and returned as plain data. |
| Error hygiene | Failures use the standard response and never expose stack traces, SQL, exception text or internal names. |

**Expected results:**
- Both kinds state the **exact message text**.
- Developer cases also state the HTTP status and the database state. Use the real shapes in [API conventions](./project-overview.md#api-conventions). A rejected request leaves no trace.
- Test data is described by fixture name and state ("CO-A with an active location LOC-A1"), never by hard-coded IDs.

**Results are not recorded here.** Execution results (actual, Pass / Fail / Not run, date, build) go in `RMS_API/specs/<feature>/test-results.md`, keyed by test ID.

**API-level developer cases come after the API design.** They need endpoint names, request fields and messages, so they are written once `api-contract.md` is approved.

**Test cases never introduce behaviour.** If a test needs an expected result the spec doesn't define, raise it as an open question in the use case first, and mark the test `Blocked: <question>`.

**Change the tests with the spec.** When a use case, data model, API contract or UI/UX changes, update the related test cases and the coverage matrix in the same change.

### Architecture Decision Records (`adr/`)

Numbered, immutable once accepted. Format:

```
# NNNN. Title

Status: Proposed | Accepted | Superseded by NNNN

## Context
## Decision
## Consequences
```

## When the conventions don't fit

If the template fights you, raise it: either deviate and note why, or propose a change to this file. Don't silently invent new section names or reorder things.
