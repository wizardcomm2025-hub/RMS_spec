# CLAUDE.md — App-Specs (RMS)

This is the `App-Specs` folder at `D:\RMS\App-Specs`. It is the single source of truth for **what** RMS (Restaurant Management System) does — covering the RMS API, the Tenant and Central databases and the Next.js admin dashboard listed in [`project-overview.md`](./project-overview.md#platforms).

**This folder contains no code.** It contains specifications, API contracts, data models, UI/UX designs, test cases and architecture decisions. Implementation happens in the sibling repos `../RMS_API` (API + SQL scripts) and `../RMS_next_js/RMS_NextJS` (UI). Implementation plans, task status and test results live in `../RMS_API/specs/<feature>/`.

> **Setup status.** Open setup items are listed in [`GETTING-STARTED.md`](./GETTING-STARTED.md).

---

## Orientation — read these first

1. `project-overview.md` — product, roles, platform decisions, **API conventions (real response shapes)** and the glossary
2. `conventions.md` — the spec-writing and test-case style guide (about authoring specs, not code)

For a specific feature, also read:

3. `features/<feature-name>/README.md` — feature summary and index of use cases
4. The relevant use-case file(s), plus `data-model.md`, `api-contract.md`, `ui-ux.md` and `test-cases/`

For anything on screen, also read `../RMS_API/specs/UI_UX_STANDARDS.md` (mandatory UI standard; §12 is the list-page recipe).

---

## Repo structure

```
App-Specs/
├── CLAUDE.md                        ← you are here
├── README.md                        ← human-facing entry point
├── GETTING-STARTED.md               ← setup status
├── tutorial.html / developer-guide.html
├── project-overview.md              ← product, roles, platform decisions, API conventions, glossary
├── conventions.md                   ← how to write a spec and its test cases
├── intake/                          ← pre-spec Q&A and client-provided info
├── features/
│   ├── template/                    ← skeleton files for a new feature
│   └── <feature-name>/
│       ├── README.md                ← feature summary + use-case index
│       ├── developer-guide.html     ← plain-language overview (HTML)
│       ├── NN-<use-case>.md
│       ├── data-model.md            ← Tenant DB / Central DB
│       ├── api-contract.md          ← endpoints, messages, auth
│       ├── ui-ux.md                 ← screen design per UI_UX_STANDARDS.md
│       ├── test-cases/
│       │   ├── README.md            ← fixtures, coverage matrix, endpoint checklist
│       │   ├── NN-<use-case>.tester.md
│       │   └── NN-<use-case>.developer.md
│       └── assets/
├── adr/
├── api/openapi.yaml                 ← placeholder; see GETTING-STARTED
├── _for-implementation-repos/CLAUDE.md
└── .claude/skills/
```

---

## Platform decisions (settled — do not reopen without an ADR)

The same table appears in `project-overview.md`; keep them in sync.

| Concern | Decision |
|---|---|
| Backend stack | .NET 6 ASP.NET Core Web API (`RMS_API`, solution `BypassDhabaAPI.sln`). Controller → Service → `ProductDBContext` → stored procedure |
| Databases | SQL Server. **Central DB** (control plane: companies, site registry, plans, SuperAdmin) + **Tenant DB** resolved from the request `Origin` host. Several companies can share one Tenant DB, so every tenant query filters on `CompanyId` |
| Data access | Stored procedures, called with `FromSqlRaw` and `SqlParameter[]` |
| Authentication | JWT bearer. `CompanyId` and `UserId` come from the token, never from the request body |
| Authorization | `[Authorize]`, `[AuthorizeExpired]`, `[SuperAdminAuthorize]` attributes on the API; menu permissions in the UI (`PERMISSIONS.<Module>.Create/Edit/Delete`) |
| API shape | `POST /{Controller}/{Method}`; body `{ Status, Message, Data }`; business failures are HTTP 200 with `Status=false` |
| Client surface | Next.js 15 admin dashboard (`RMS_NextJS`), MUI / Bazaar template, responsive 360–1920 px |
| Supported languages | <!-- TODO: confirm from intake (i18next is installed in the UI) --> |

---

## Before a spec exists — use `intake/`

Capture what you know in `intake/<topic-slug>.md`. The [`spec-intake`](./.claude/skills/spec-intake/SKILL.md) skill runs the interview and writes Q&A as it goes. See [`intake/README.md`](./intake/README.md).

## How to write a new feature spec

1. Copy `features/template/` to `features/<name>/`
2. Start with `README.md` — summary, use-case table, feature code for test IDs
3. One file per use case, numbered (`01-`, `02-`, …), following `conventions.md`
4. Add `data-model.md` and `api-contract.md` once the data and API shape is clear
5. Add `ui-ux.md` for any feature with an admin screen — the 10-point UI/UX section required by `UI_UX_STANDARDS.md`
6. Add `developer-guide.html` (HTML, not markdown; copy the template)
7. Add `test-cases/`: one tester and one developer file per use case, plus `README.md`
8. Get user approval, then plan in `../RMS_API/specs/<name>/plan.md` and `tasks.md`
9. Update the module's section and Index row in `../RMS_API/specs/MODULES.md`

## How to edit an existing spec

State: what is changing and why; which layers are affected; **breaking** or **additive**; which test cases were added, updated or marked `Removed`. A behaviour change always updates its related test cases in the same change.

---

## Rules for writing specs

**One use-case per file.** Do not combine multiple user flows in one file.

**Never duplicate behaviour — link instead.**

**Open questions stay in the document.** When resolved, mark them resolved with the answer and the date — never delete them.

**Platform-specific behaviour goes in Platform notes** — the fixed RMS sub-sections from `conventions.md`: RMS API, Data — Tenant DB, Data — Central DB, Integrations, Admin dashboard.

**Acceptance criteria are mandatory**, and specific enough that a tester can derive pass/fail cases without asking. For admin screens they include responsive and feedback-state checks (`UI_UX_STANDARDS.md`).

**Test cases change with the spec.** Tester (UI) and developer (API) cases per use case, under `features/<name>/test-cases/`. Test **definitions** live here; **actual results** are recorded in `../RMS_API/specs/<name>/test-results.md`.

**Prerequisites are mandatory.** State `none` explicitly when there are none.

**Every feature ships with a `developer-guide.html`.**

---

## For Claude Code

- Do not generate code in this folder. This is a documentation folder.
- When asked to write a spec, read `conventions.md` in full first.
- When asked to update a spec, read the existing file in full first.
- When something is ambiguous, check `project-overview.md` (glossary) first. If that doesn't resolve it, stop and ask. Do not guess.
- For data-model facts, read the SQL in `../RMS_API/ProductManagement.API/Database Scripts/` (`Stored Procedure/`, `Table/`, `Tenant/`, `CentralDB/`, `Function/`, `UserDefinedFunction/`) and the DB dumps the user provides. If a script isn't there, ask the user for it.
- For real API behaviour (status codes, messages), read the controller, the attributes in `../RMS_API/ProductManagement.API/Helpers/` and `MultiTenancy/TenantResolutionMiddleware.cs`. Never invent a response a test expects; raise an open question instead.
- Preserve the history of open questions.
- This folder is not a git repository yet. Do not create branches or commits here unless the user asks.
