# Setting → Driver Master

A **Driver** is a person who drives company vehicles to deliver orders or move stock between locations (outlets, warehouses, central kitchens). When a supplying location dispatches stock in **Inbound Requisition**, it picks a vehicle from Vehicle Master and a driver from Driver Master; the transfer keeps that driver (`TransferVehicleRelation.DriverId`). Each company keeps its own list in the Tenant DB table `dbo.DriverMaster`.

The screen lets a company admin list and search drivers, add them with their licence number and an optional photo, edit them, and switch a driver Active or Inactive. Drivers are never deleted, so past transfers keep their driver.

Status (2026-10-29): **Approved (spec written with Antigravity, corrected and confirmed 2026-10-29) — API and UI implemented (builds pass); SQL written, not yet deployed; not tested.** Plan: [`RMS_API/specs/driver-master/plan.md`](../../../RMS_API/specs/driver-master/plan.md).

Feature code (used in test IDs): `DRV`

## Scope

- Fix the defects below (cross-company overwrite and the anonymous licence check first).
- Page on the list recipe (`UI_UX_STANDARDS.md` §12) — already built with Antigravity; aligned to the API here.
- Server-side rules and the API patterns used by Department / Vehicle Master.
- Inbound Requisition dispatch offers active drivers only.

## Out of scope

- Assigning a vehicle to a driver permanently (chosen per dispatch).
- Licence expiry dates (no column).
- Driver mobile app, GPS.
- Moving photos out of the database into file storage.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-drivers.md`](./01-view-and-search-drivers.md) | See every driver of the company; search by name or licence; filter by status |
| 02 | [`02-add-driver.md`](./02-add-driver.md) | Add an active driver with a unique licence number and optional photo |
| 03 | [`03-edit-driver.md`](./03-edit-driver.md) | Change a driver's name, licence number or photo |
| 04 | [`04-switch-driver-on-off.md`](./04-switch-driver-on-off.md) | Deactivate or reactivate a driver instead of deleting them |

## Key concepts

- **Licence number (`LicNo`)** — driving licence id; stored trimmed and uppercase; unique per company among active and inactive drivers. Shown in the UI as "License Number".
- **Profile picture** — optional JPG / PNG up to 1 MB, stored as a data URL in `DriverMaster.ProfileImage`.
- **Active / Inactive** — inactive drivers aren't offered in dispatch; past transfers keep them.

## Data, API, UI and tests

- Data model: [`data-model.md`](./data-model.md)
- API contract: [`api-contract.md`](./api-contract.md)
- UI / UX: [`ui-ux.md`](./ui-ux.md)
- Test cases: [`test-cases/README.md`](./test-cases/README.md)
- Plan, tasks, test results: `RMS_API/specs/driver-master/`
- Module explainer: `RMS_API/specs/MODULES.md` → Driver Master

## Defects this feature fixes

| # | Defect | Fixed by |
|---|---|---|
| D1 | Add / edit showed "Vehicle updated successfully" (copy-paste) — fixed in the Antigravity UI | [02](./02-add-driver.md), [03](./03-edit-driver.md) |
| D2 | A duplicate licence returns `status: true` with `saveStatus: false` and an empty message | [02](./02-add-driver.md), [03](./03-edit-driver.md) |
| D3 | **Cross-company overwrite:** `SP_AddEditDriver` updates `WHERE DriverId = @DriverId` with no company filter — any company that knows another company's driver id can rename it, change its licence and photo, or switch it off (found 2026-10-29 in review) | [03](./03-edit-driver.md) |
| D4 | **`/Users/ValidateLicNo` has no `[Authorize]`** and takes `CompanyId` from the body: anyone can probe any company's licence numbers. The UI never sends `CompanyId`, so the live check never finds a duplicate; the check also ignores inactive drivers | [02](./02-add-driver.md) |
| D5 | No server-side permission checks; SuperAdmin tokens pass `[Authorize]`; exception text returned to the caller; `GETDATE()` instead of UTC; an edit doesn't record `LastUpdatedBy/On`; no stale-save check | all |
| D6 | `DeleteDriver` hard-deletes; Inbound Requisition's dispatch lists **inactive** drivers | [04](./04-switch-driver-on-off.md) |
| D7 | The status switch saves through the full add/edit call with the list row's values (incl. the photo), which can overwrite a newer edit | [04](./04-switch-driver-on-off.md) |
| D8 | No photo size or type check: any size of text is stored and sent with every list load | [02](./02-add-driver.md) |
| D9 | Dead `payout-row.tsx`, a hex colour in `driver-modal.tsx`, About text promising features that don't exist (fixed in the Antigravity UI) | [`ui-ux.md`](./ui-ux.md) |

## Depends on

- **User Management → Roles** — `create_driver_master` / `edit_driver_master`.
- **Setting → Vehicle Master** — same dispatch dialog; same API patterns.
- **Stock → Inbound Requisition** — consumer of the active list.

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | No delete; the API refuses it and the UI has none | 2026-10-29 |
| Q2 | Licence number: trimmed, uppercase, letters, digits, spaces and hyphens, 1–50; unique per company among active **and** inactive drivers | 2026-10-29 |
| Q3 | Driver name: letters, numbers, spaces and `& - ( ) ' / . ,`, trimmed, 1–100 (same as Department); not unique | 2026-10-29 |
| Q4 | Photo optional: JPG or PNG, **1 MB** max; the UI refuses bigger files and the API refuses anything else | 2026-10-29 |
| Q5 | Dispatch (Inbound Requisition) offers **active drivers only** (`activeOnly` on the list API) | 2026-10-29 |
| Q6 | Switching off is always allowed | 2026-10-29 |
| Q7 | Same API patterns as Department: `[CompanyAuthorize]`, server permissions, generic error, `LastUpdatedOn` edit token, status through its own endpoint | 2026-10-29 |

## Cross-cutting open questions

None.
