# Setting → Location

A **Location** is a physical place a company operates: an outlet, a warehouse or a central kitchen. It anchors stock, users, requisitions, wastage, PO receive and POS. In the database the entity is **Warehouse** (`dbo.Warehouse`); the UI calls it **Location**. This screen lets a company admin list, search and filter locations, add and edit them, assign employees, set the Default Location and Can Take Requisition flags, and switch a location Active or Inactive. Locations are never deleted from the UI.

Status (2026-10-28): **Approved.** UI/UX phase built (not fully browser-verified); API/DB phase pending. Progress: [`RMS_API/specs/setting-location/tasks.md`](../../../RMS_API/specs/setting-location/tasks.md).

Feature code (used in test IDs): `LOC`

## Scope

- Fix the existing defects listed below.
- Replace delete with an Active / Inactive switch, and block the legacy delete endpoint.
- Server-side permission checks, a generic error message, and refusal of stale edits (LOC-Q1, Q2, Q7).
- The list page is the approved reference design for every list page (`UI_UX_STANDARDS.md` §12).

## Out of scope

- Server-side pagination and new fields.
- Managing bins (a location can have bins; this screen never changes them).
- Removing the `DeleteWareHouseDetails` action from code — it is blocked instead (LOC-Q8).
- Other screens that read `Warehouse` without an `IsActive` filter (plan risk R2).
- The customer storefront.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-filter-locations.md`](./01-view-and-filter-locations.md) | See every location of the company, search and filter it, with all feedback states |
| 02 | [`02-add-location.md`](./02-add-location.md) | Create a new active location with type, employees, address and flags |
| 03 | [`03-edit-location.md`](./03-edit-location.md) | Load a location by id and save changes without touching bins or ownership |
| 04 | [`04-switch-location-on-off.md`](./04-switch-location-on-off.md) | Deactivate or reactivate a location instead of deleting it |
| 05 | [`05-default-location.md`](./05-default-location.md) | Mark at most one active location per company as Default |

## Key concepts

Location, Location Type, Bin, Active / Inactive / Deleted, Requisition and POS Outlet are defined in the [glossary](../../project-overview.md#glossary).

- **POS Outlet** is not on the form. Every save stores `IsPOSOutlet = 1`, and saving one location never clears the flag on others (decision Q1).

## Data, API, UI and tests

- Data model: [`data-model.md`](./data-model.md)
- API contract: [`api-contract.md`](./api-contract.md)
- UI / UX: [`ui-ux.md`](./ui-ux.md)
- Test cases: [`test-cases/README.md`](./test-cases/README.md)
- Plan, tasks, test results: `RMS_API/specs/setting-location/` (`plan.md`, `tasks.md`, `test-results.md`)
- Module explainer: `RMS_API/specs/MODULES.md` → Location
- History: `RMS_API/specs/setting-location/spec.legacy.md` (the pre-migration spec, superseded)

## Defects this feature fixes

| # | Defect (as found 2026-10-28) | Fixed by |
|---|---|---|
| D1 | Save hard-codes `IsPOSOutlet = true` and the SP clears the POS flag on every other location | [02](./02-add-location.md), [03](./03-edit-location.md) |
| D2 | Duplicate name on edit returns `Status=true` with `Response="Exist"`; the UI shows success and nothing is saved | [03](./03-edit-location.md) |
| D3 | `GetWareHouseDetailsById` has no `[Authorize]`; a missing row throws an index error | [03](./03-edit-location.md) |
| D4 | Every save sets `BinWarehouse.IsActive = 0` for the location | [02](./02-add-location.md), [03](./03-edit-location.md) |
| D5 | `UserResponsible` is overwritten with the editing user | [03](./03-edit-location.md) |
| D6 | Name not trimmed; UI limit 35, no API limit | [02](./02-add-location.md) |
| D8 | `GetAllLocationDDl` returns the string `"Error"` on failure; UI load failures only logged | [01](./01-view-and-filter-locations.md) |
| D9 | Page title "Warehouse - RMS"; dialog X closes while submitting | [`ui-ux.md`](./ui-ux.md) |

## Depends on

- **User Management → Roles** — the `Location.Create` / `Location.Edit` permissions.
- **Company setup** — the company's Location Types (`Map_Company_LocationType`).

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | Multiple POS outlets allowed; not on the form; always saved as true | 2026-10-28 |
| Q2 | No delete; Active / Inactive switch instead | 2026-10-28 |
| Q3 | Multiple bins per location; saving never changes bins; no bin UI | 2026-10-28 |
| Q4 | Location Name max 100 characters | 2026-10-28 |
| Q5 | Pincode optional; if given, exactly 6 digits | 2026-10-28 |
| Q6 | Default Location optional; at most one per company | 2026-10-28 |
| Q7 | Scope = defects above + the switch | 2026-10-28 |
| LOC-Q1 – Q11 | Security, validation and concurrency rules — see below | 2026-10-28 |

## Cross-cutting open questions

All resolved with the user on 2026-10-28. Kept for history.

- **LOC-Q1** Must the API enforce `Location.Create` / `Location.Edit`, or only the UI? _Resolved 2026-10-28: the API enforces them. Add needs `Location.Create`; edit and status change need `Location.Edit`. Without it: `Status=false`, "You don't have permission to perform this action.", nothing written._ Wording aligned with the UI's existing text (apostrophe form) on 2026-10-28.
- **LOC-Q2** What message does an endpoint return for an unexpected error? _Resolved 2026-10-28: `Status=false`, "Something went wrong. Please try again."; the exception detail goes only to the log4net log._
- **LOC-Q3** Confirm the API validation messages. _Resolved 2026-10-28: accepted — "Location Name is required", "Location Name must be 100 characters or less", "Pincode must be 6 digits", and "Activate the location to make it default" when an inactive location is saved as Default (save refused)._
- **LOC-Q4** Should `POST /Product/GetUserWarehouse` require a token? _Resolved 2026-10-28: yes, `[Authorize]`; no token → 401._ Correction 2026-10-28: the action already has `[Authorize]` in code (the old spec table was wrong), so no change is needed beyond rejecting SuperAdmin tokens.
- **LOC-Q5** How does the Location screen get inactive rows without showing them on the other screens that call `GetWareHouseDetails` (8 active callers, verified 2026-10-28)? _Resolved 2026-10-28: optional request field `IncludeInactive` (bool, default false). Only the Location screen sends true; other callers keep receiving active rows only._
- **LOC-Q6** Employees in a save that are not active staff of the caller's company? _Resolved 2026-10-28: reject the save — `Status=false`, "One or more employees are not valid.", nothing written._ Applied to employees **newly added** by the save; employees already mapped to that location are accepted as they are, so the approved "keep unresolvable mappings on edit" rule in [`ui-ux.md`](./ui-ux.md#3-dialog--form) still holds (interpretation, 2026-10-28).
- **LOC-Q7** Two admins edit the same location at once? _Resolved 2026-10-28: refuse the stale save. Get-by-id returns `UpdatedAt`; an edit sends it back; if the row changed since (or `UpdatedAt` is missing on an edit), `Status=false`, "This location was changed by someone else. Reload and try again.", nothing written._
- **LOC-Q8** What happens to the legacy `POST /Setting/DeleteWareHouseDetails`? _Resolved 2026-10-28: blocked now — it returns `Status=false`, "Locations cannot be deleted. Deactivate the location instead." and writes nothing. The action stays in code; the UI never calls it._
- **LOC-Q9** A save with a `WarehouseId` that doesn't exist, is deleted, or belongs to another company? _Resolved 2026-10-28: `Status=false`, "Location not found", nothing written, nothing about the other company revealed._
- **LOC-Q10** A save with a Location Type that is not one of the company's active types? _Resolved 2026-10-28: `Status=false`, "Location Type is required" (same text as the empty field), nothing written._
- **LOC-Q11** A SuperAdmin token on the Location endpoints with a company's `Origin`? _Resolved 2026-10-28: rejected with 401 `{ "message": "Unauthorized" }`, nothing returned or written. SuperAdmins reach company data only through Support login-as-company._
