# Setting → Department

A **Department** is a team staff belong to — Kitchen, Service, Bar, Housekeeping. Each user has one department; roster patterns and rosters are planned per department; requisitions, receiving, assets and checklists can refer to one. Each company keeps its own list in the Tenant DB table `dbo.DepartmentMaster`.

The screen lets a company admin list and search departments, add and rename them, and switch a department Active or Inactive. Departments are never deleted.

Status (2026-10-29): **Approved — API and UI implemented (builds pass); SQL written, not yet deployed; not tested.** Plan: [`RMS_API/specs/department/plan.md`](../../../RMS_API/specs/department/plan.md).

Feature code (used in test IDs): `DEP`

## Scope

- Fix the defects below (the 10-row list limit first).
- Rebuild the page on the list recipe (`UI_UX_STANDARDS.md` §12, reference: Setting → Location) with a module dialog like Location's.
- Server-side rules and the API patterns used by Location and Category Master.

## Out of scope

- Moving users or roster patterns between departments (done in User List / Roster Pattern).
- Department managers, codes or other new fields.
- The shared `SingleFieldModal` (still used by Brands, Menu Section, Reason of Wastage, Wastage Entry) — not changed.
- Roster screen code — it keeps calling the department list, which now returns every department (see D1).

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-departments.md`](./01-view-and-search-departments.md) | See every department of the company; search and filter by status |
| 02 | [`02-add-department.md`](./02-add-department.md) | Create an active department |
| 03 | [`03-rename-department.md`](./03-rename-department.md) | Change a department's name |
| 04 | [`04-switch-department-on-off.md`](./04-switch-department-on-off.md) | Deactivate or reactivate a department instead of deleting it |

## Key concepts

Department — see the [glossary](../../project-overview.md#glossary).

## Data, API, UI and tests

- Data model: [`data-model.md`](./data-model.md)
- API contract: [`api-contract.md`](./api-contract.md)
- UI / UX: [`ui-ux.md`](./ui-ux.md)
- Test cases: [`test-cases/README.md`](./test-cases/README.md)
- Plan, tasks, test results: `RMS_API/specs/department/`
- Module explainer: `RMS_API/specs/MODULES.md` → Department

## Defects this feature fixes (found 2026-10-29)

| # | Defect | Fixed by |
|---|---|---|
| D1 | `SP_GetAllDepartment` pages on the server (default 10 rows) ordered by the random GUID; the UI never sends a page number, so only 10 departments ever load — on this page and in **Roster**, which calls the same list | [01](./01-view-and-search-departments.md) |
| D2 | A duplicate save returns `status: false` with an **empty** message; the UI ignores `status` and shows "Department created successfully" while nothing is saved | [02](./02-add-department.md), [03](./03-rename-department.md) |
| D3 | The duplicate check isn't trimmed and only looks at active departments: "Sales" can be added while an inactive "Sales" exists | [02](./02-add-department.md) |
| D4 | Saving another company's id changes nothing (the update is company-filtered) but reports success; get-by-id returns deleted rows | [03](./03-rename-department.md) |
| D5 | No server-side permission checks; SuperAdmin tokens pass `[Authorize]`; exception text returned to the caller; dates saved with `GETDATE()` (server local time) instead of UTC | all |
| D6 | The status switch saves immediately with no confirmation, through the full save call | [04](./04-switch-department-on-off.md) |
| D8 | Get-by-id never worked: `SP_GetDepartmentById` maps to the same entity as the list but doesn't return `TotalRecord`, so EF throws on every call; the UI's `catch` silently used the list row instead (found 2026-10-29 while implementing) | [03](./03-rename-department.md) |
| D9 | The live name check sends `departmentMasterId`, but the API model expects `DeptMasterId`, so on edit the department's own name can show as a duplicate (found 2026-10-29) | [03](./03-rename-department.md) |
| D7 | Page not on the list recipe: full-page loader, client pagination of the 10 rows, search only after 3 characters, no load-error / empty / no-match states, no phone layout; browser tab "Department - Bazaar Next.js E-commerce Template"; unused `payment-row.tsx`, `types.ts`; the About link to User List never shows (its key "User List" isn't in the text) | [`ui-ux.md`](./ui-ux.md) |

## Depends on

- **User Management → Roles** — `create_department` / `edit_department` permissions.
- **Setting → Location** — the shared `[CompanyAuthorize]`, `IPermissionService.HasPermissionAsync` and the stale-save pattern.

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | A department can't be switched off while **active users** or **active roster patterns** belong to it | 2026-10-29 |
| Q2 | Name: letters, numbers, spaces and `& - ( ) ' / . ,`, trimmed, 1–100 characters; unique per company among non-deleted departments (active and inactive), case-insensitive | 2026-10-29 |
| Q3 | No delete; the API refuses it and the UI has none | 2026-10-29 |
| Q4 | The list API returns every non-deleted department, sorted by name; search and filter in the browser; no pagination | 2026-10-29 |
| Q5 | Roster keeps calling the list API (now complete, inactive included so old rosters stay filterable) instead of the active-only dropdown — adjusted from the Q4 suggestion during spec writing | 2026-10-29 |
| Q6 | Same API patterns as Location and Category Master: `[CompanyAuthorize]`, server permissions, generic error, `UpdatedAt` edit token, status through its own endpoint | 2026-10-29 |

## Cross-cutting open questions

None.
