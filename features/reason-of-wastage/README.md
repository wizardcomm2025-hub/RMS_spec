# Setting → Reason of Wastage

A **Reason of Wastage** is a reason staff pick when they write off stock in **Stock → Wastage**: Expired, Spoiled, Damaged in transit, Over-cooked. Each wastage line keeps the reason it was saved with (`Wastage.ReasonMasterId`), and the **Wastage Report** totals by reason. Each company keeps its own list in the Tenant DB table `dbo.WastageReasonMaster`; new companies get a starter set at onboarding (copied from `DefaultWastageReasonMaster`).

The screen lets a company admin list and search reasons, add and rename them, and switch a reason Active or Inactive. Reasons are never deleted. Wastage Entry offers only active reasons and keeps its "＋" quick-add.

Status (2026-10-29): **Approved — API and UI implemented (builds pass); SQL written, not yet deployed; not tested.** Plan: [`RMS_API/specs/reason-of-wastage/plan.md`](../../../RMS_API/specs/reason-of-wastage/plan.md).

Feature code (used in test IDs): `RSN`

## Scope

- Fix the defects below.
- Rebuild the page on the list recipe (`UI_UX_STANDARDS.md` §12, reference: Setting → Location) with a module dialog like Department's.
- Wastage Entry: active reasons only; quick-add uses the same rules and shows the real result.
- Server-side rules and the API patterns used by Location, Category Master and Department.

## Out of scope

- Photo-required or manager-approval rules per reason (the old About text promised them; they don't exist).
- Changing how Wastage is saved, or refusing an inactive reason inside the Wastage save SP (see [04](./04-switch-reason-on-off.md) → Out of scope).
- The Wastage Report (it reads `Wastage` joined to the reason; unchanged).
- `DefaultWastageReasonMaster` and onboarding.
- The shared `SingleFieldModal` (still used by Brands, Menu Section) — not changed; this page and Wastage Entry stop using it for reasons.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-reasons.md`](./01-view-and-search-reasons.md) | See every reason of the company; search and filter by status |
| 02 | [`02-add-reason.md`](./02-add-reason.md) | Create an active reason — here or from Wastage Entry's quick-add |
| 03 | [`03-rename-reason.md`](./03-rename-reason.md) | Change a reason's text |
| 04 | [`04-switch-reason-on-off.md`](./04-switch-reason-on-off.md) | Deactivate or reactivate a reason instead of deleting it |

## Key concepts

Reason of Wastage — see the [glossary](../../project-overview.md#glossary).

## Data, API, UI and tests

- Data model: [`data-model.md`](./data-model.md)
- API contract: [`api-contract.md`](./api-contract.md)
- UI / UX: [`ui-ux.md`](./ui-ux.md)
- Test cases: [`test-cases/README.md`](./test-cases/README.md)
- Plan, tasks, test results: `RMS_API/specs/reason-of-wastage/`
- Module explainer: `RMS_API/specs/MODULES.md` → Reason of Wastage

## Defects this feature fixes (found 2026-10-29)

| # | Defect | Fixed by |
|---|---|---|
| D1 | Wastage Entry loads reasons with `OpenMode: "All"`, so **switched-off reasons can be picked** when writing off stock | [04](./04-switch-reason-on-off.md) |
| D2 | Wastage Entry re-loads the reason dropdown with the **item search text** (debounced, ≥ 3 characters), so reasons disappear from the dropdown while searching items | [01](./01-view-and-search-reasons.md) |
| D3 | Wastage Entry's quick-add ignores the result: a duplicate still shows "Wastage reason added."; it lets the user add an inactive reason; the "＋" is shown on the Wastage *save* permission, not `create_reason_of_wastage` | [02](./02-add-reason.md) |
| D4 | The duplicate check counts **deleted** rows (a deleted "Expired" blocks a new one forever) and doesn't trim (" Expired" is accepted next to "Expired"); a duplicate returns `Allready_Exit` with `Status=true` | [02](./02-add-reason.md), [03](./03-rename-reason.md) |
| D5 | Saving an unknown, deleted or other company's id changes nothing but reports `OK`; get-by-id returns deleted rows; the edit save also overwrites `IsActive` | [03](./03-rename-reason.md) |
| D6 | The status switch saves immediately with no confirmation; `WestageReasonActive` reports `OK` for any id, including another company's; on failure the switch still flips | [04](./04-switch-reason-on-off.md) |
| D7 | No server-side permission checks; SuperAdmin tokens pass `[Authorize]`; exception text returned to the caller; dates saved with `GETDATE()` (server local time); no concurrency guard; `DeleteWastageById` soft-deletes although the UI's delete is switched off | all |
| D8 | List is server-paged (10 rows) with a **guessed** total ("page × 10 + 1"), search only from 3 characters, full-page loader, no load-error / empty / no-match states, no phone layout | [01](./01-view-and-search-reasons.md), [`ui-ux.md`](./ui-ux.md) |
| D9 | About text promises "photo required, manager approval" rules that don't exist; uses `SingleFieldModal` with a delete-forever icon; dead `types.ts`, commented-out delete | [`ui-ux.md`](./ui-ux.md) |

## Depends on

- **User Management → Roles** — `create_reason_of_wastage` / `edit_reason_of_wastage` permissions.
- **Setting → Location / Department** — the shared `[CompanyAuthorize]`, `IPermissionService.HasPermissionAsync` and the stale-save pattern.
- **Stock → Wastage** — consumer of the active list and the quick-add.

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | Wastage Entry lists **active reasons only** and keeps the "＋" quick-add; quick-add always creates an Active reason, follows the same rules, needs `create_reason_of_wastage` and shows the real result | 2026-10-29 |
| Q2 | Reason text: trimmed, required, letters (any language), numbers, spaces and `& - ( ) ' / . ,`; **no maximum length** ("no max limit in this case" — the column is `nvarchar(max)`); unique per company among non-deleted reasons (active and inactive), case-insensitive | 2026-10-29 |
| Q3 | Switching off is **always allowed** — past wastage keeps its reason; the reason just can't be picked any more | 2026-10-29 |
| Q4 | No delete; the API refuses it and the UI has none | 2026-10-29 |
| Q5 | About text: "These are the reasons staff pick when they write off stock in Wastage. Switched-off reasons can't be picked, and past wastage keeps its reason. The Wastage Report totals by reason." | 2026-10-29 |
| Q6 | The list API returns every non-deleted reason (or only active ones for `OpenMode: "Active"`), sorted; search and filter in the browser; no pagination | 2026-10-29 |
| Q7 | Same API patterns as Department: `[CompanyAuthorize]`, server permissions, generic error, edit token, status through `WestageReasonActive` (route kept, logic fixed) | 2026-10-29 |

## Cross-cutting open questions

None. (RSN-Q1 resolved 2026-10-29: same character rule as Department, no length cap — approved as written.)
