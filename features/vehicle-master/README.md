# Setting → Vehicle Master

A **Vehicle** is a car, van, or truck used for deliveries and stock transfers between Locations (outlets, warehouses, central kitchens). Each vehicle has a unique Registration Number and a Car Manufacturer / Brand Name. When supplying locations dispatch items in **Inbound Requisition** or **Stock Transfer**, they assign a vehicle from Vehicle Master along with a driver from Driver Master.

Each company manages its own vehicles in the Tenant DB table `dbo.VehicleMaster`. The screen lets a company admin list and search vehicles, add and edit them, and switch a vehicle Active or Inactive. Vehicles are never hard deleted to preserve historical transfer logs and audit integrity.

Status (2026-10-29): **Approved (spec written with Antigravity, corrected and confirmed 2026-10-29) — API and UI implemented (builds pass); SQL written, not yet deployed; not tested.** Plan: [`RMS_API/specs/vehicle-master/plan.md`](../../../RMS_API/specs/vehicle-master/plan.md).

Feature code (used in test IDs): `VEH`

## Scope

- Fix the defects below (hard delete, missing company isolation, typo routes, and un-scoped queries).
- Rebuild the page on the list recipe (`UI_UX_STANDARDS.md` §12, reference: Setting → Location) using `PageHeader` without duplicate short description.
- Introduce `IsActive` toggle switch with confirmation dialog (matching Department & Location), deprecating hard deletes.
- Multi-field client search filtering across both Registration Number and Car Manufacturer Name.
- Live registration number uniqueness check via `ValidateRegNo`.
- Server-side validation, `[CompanyAuthorize]`, and permission enforcement (`create_vehicle_master`, `edit_vehicle_master`).

## Out of scope

- Driver-to-vehicle permanent assignment (assigned dynamically per transfer / dispatch).
- Vehicle document tracking (insurance policy, fitness certificate upload, PUC) — planned for a future release.
- GPS / real-time tracking integration.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-vehicles.md`](./01-view-and-search-vehicles.md) | See every vehicle of the company; search by registration number or manufacturer; filter by status |
| 02 | [`02-add-vehicle.md`](./02-add-vehicle.md) | Register a new active vehicle with unique registration number |
| 03 | [`03-edit-vehicle.md`](./03-edit-vehicle.md) | Update registration number or manufacturer name |
| 04 | [`04-switch-vehicle-on-off.md`](./04-switch-vehicle-on-off.md) | Deactivate or reactivate a vehicle instead of deleting it |

## Key concepts

- **Registration Number (`RegNo`):** Official vehicle registration / license plate number (e.g. `MH12AB1234`), uppercase normalized, unique per company.
- **Car Manufacturer Name (`BrandName`):** Brand or make of the vehicle (e.g. `Toyota`, `Tata`, `Mahindra`).
- **Vehicle Status (`IsActive`):** When Active (`1`), offered in transfer dispatch dropdowns. When Inactive (`0`), hidden from dispatch dropdowns but preserved in past transfer records.

## Data, API, UI and tests

- Data model: [`data-model.md`](./data-model.md)
- API contract: [`api-contract.md`](./api-contract.md)
- UI / UX: [`ui-ux.md`](./ui-ux.md)
- Test cases: [`test-cases/README.md`](./test-cases/README.md)
- Module explainer: `RMS_API/specs/MODULES.md` → Vehicle Master

## Defects this feature fixes (found 2026-10-29)

| # | Defect | Fixed by |
|---|---|---|
| D1 | `SP_DeleteVehicle` performs an un-scoped hard `DELETE FROM VehicleMaster WHERE VehicleId=@VehicleId` with no company filter; breaks foreign keys or historical transfers | [04](./04-switch-vehicle-on-off.md) |
| D2 | Endpoints use misspelled routes (`GetVechileList`, `AddEditVechile`); inconsistent naming | [api-contract.md](./api-contract.md) |
| D3 | No server-side permission checks; SuperAdmin tokens pass `[Authorize]`; missing `[CompanyAuthorize]` | all |
| D4 | `SP_AddEditVehicle` writes `GETDATE()` (local server time) instead of `GETUTCDATE()`; no concurrency/stale check | [02](./02-add-vehicle.md), [03](./03-edit-vehicle.md) |
| D5 | UI search only filters on `brandName`, ignoring `regNo` completely | [01](./01-view-and-search-vehicles.md), [`ui-ux.md`](./ui-ux.md) |
| D6 | UI uses legacy layout with redundant helper text (`description`), no status chip, and delete button commented out | [`ui-ux.md`](./ui-ux.md) |
| D7 | Live registration validation doesn't trim spaces and only looks at active vehicles; a duplicate save returns `status: true` with `saveStatus: false`, so the page shows "Vehicle created successfully" while nothing is saved | [02](./02-add-vehicle.md) |
| D8 | **Cross-company overwrite:** `SP_AddEditVehicle` updates `WHERE VehicleId = @VehicleId` only and sets `CompanyId = @CompanyId`, so any company that knows another company's vehicle id can rename it and move it into its own company (found 2026-10-29 in review) | [03](./03-edit-vehicle.md) |
| D9 | Inbound Requisition's dispatch dialog lists **inactive** vehicles (it uses the full list) | [04](./04-switch-vehicle-on-off.md) |
| D10 | The UI status switch saves through the full add/edit call with the list row's values, which can overwrite a newer edit | [04](./04-switch-vehicle-on-off.md) |

## Depends on

- **User Management → Roles** — `create_vehicle_master` / `edit_vehicle_master` permissions.
- **Stock → Inbound Requisition / Stock Transfer** — consumers of the active vehicle list.
- **Setting → Location** — the shared `[CompanyAuthorize]`, `IPermissionService.HasPermissionAsync` and stale-save patterns.

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | Hard delete is **permanently blocked**. Switching off (`IsActive = 0`) is used instead to protect transfer history | 2026-10-29 |
| Q2 | `RegNo` is required, trimmed, uppercase, letters/numbers only, 1–20 characters; unique per company among non-deleted vehicles (active and inactive), case-insensitive | 2026-10-29 |
| Q3 | `BrandName` is required, trimmed, 1–100 characters; letters, numbers, spaces, and `& - ( ) ' / . ,` | 2026-10-29 |
| Q4 | Header uses `PageHeader` with title, About panel, and **no duplicate short description** | 2026-10-29 |
| Q5 | List returns all company vehicles; search and status filtering are client-side; no server pagination needed | 2026-10-29 |
| Q6 | Same API patterns as Department & Location: `[CompanyAuthorize]`, standard `{ status, message, data }`, sanitized error messages | 2026-10-29 |
| Q7 | Dispatch (Inbound Requisition) offers **active vehicles only**: the list API takes an optional `activeOnly` flag | 2026-10-29 |
| Q8 | Q2 confirmed by the user: letters and digits only, max 20, stored uppercase; the form removes spaces and hyphens as the user types; unique among active **and** inactive vehicles | 2026-10-29 |
