# Data model — Setting → Location

Locations live in the Tenant DB table `dbo.Warehouse`, one row per location, scoped by `CompanyId`. Employees are linked through `UserWarehouseMapping`, bins through `BinWarehouse`, and each company's location types come from `Map_Company_LocationType`. The feature changes four stored procedures and adds one; **no table schema changes**.

> SQL scripts: `RMS_API/ProductManagement.API/Database Scripts/Stored Procedure/` (new files, `CREATE OR ALTER`). Facts below were checked against the dev dump `RMS_API_DEV281026.sql` (RMS_Shared_Dev, 2026-10-28).

## Tenant DB

### Tables

| Table | Change | Primary key | Purpose |
|---|---|---|---|
| `Warehouse` | Existing (behaviour of `IsActive` changes) | `WarehouseId` | One location of a company |
| `UserWarehouseMapping` | Existing | — | Employees assigned to a location |
| `BinWarehouse` | Existing, must not change | — | Bins of a location; this feature never writes it |
| `Map_Company_LocationType` | Existing, read only | — | The company's location types |

### Columns `Warehouse` relies on

| Column | Notes |
|---|---|
| `WarehouseId` | PK |
| `CompanyId` | Company boundary; every read and write filters on the token's company |
| `WarehouseName` | Location Name; trimmed, 1–100 characters (column allows 500); unique per company among `IsDeleted = 0` rows, case-insensitive |
| `WarehouseType` | Location Type name from `Map_Company_LocationType` |
| `AddressLine1`, `AddressLine`, `City`, `StateName`, `Zip` | Address; optional, ≤ 500; `Zip` empty or 6 digits |
| `IsDefault` | At most one `1` per company; only on an active row |
| `CanTakeRequisition` | Stored as sent |
| `IsPOSOutlet` | Always `1` on save; saving never clears it on other rows |
| `IsActive`, `IsDeleted` | See lifecycle |
| `UserResponsible`, `CreatedBy`, `CreatedAt` | Set on insert to the caller; never changed on update |
| `UpdatedBy`, `UpdatedAt` | Set on every update and status change. `UpdatedAt` is also the edit token for the stale-save check (LOC-Q7) |

- **Checked in the SP:** `CompanyId` match on every read and write; name uniqueness; type belongs to the company.

### Address mapping

UI "Address Line 1" → `AddressLine1`; UI "Address Line 2" → `AddressLine` (the UI, service and SP each swap the two and the swaps cancel out). The by-id SP returns them as `Address` (line 1) and `AddressLine1` (line 2). Unchanged in this scope.

### Stored procedures

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `Sp_GetWareHouseandBinDetails` | Altered | `@CompanyId, @IncludeInactive bit = 0` | `@IncludeInactive = 1`: `IsDeleted = 0` rows, active first then name. `0` (default, existing callers): `IsActive = 1` as today. Returns `IsActive` |
| `GetWarehouseBinDetailsbyWarehouseId` | Altered | `@CompanyId, @UserId, @WarehouseId` | By id: `IsDeleted = 0`, returns `IsActive` and `UpdatedAt`; no row when not this company's |
| `SelectedEmployeeByWarehouse` | Existing | `@CompanyId, @UserId, @WarehouseId` | Employees of a location |
| `SP_ValidateLocName` | Altered | `@LocName, @CompanyId, @LocationId` | Duplicate check on trimmed name among `IsDeleted = 0` (active and inactive), excluding `@LocationId` |
| `Sp_SaveWareHouseDetails` | Altered | `@WarehouseId, …, @CompanyId, @UserId, @IsDefault, …, @BinNumber, @EmployeeDetails, @CanTakeRequisition, @IsPOSOutlet, @UpdatedAt` | Trim name; on edit: row must exist for `@CompanyId` with `IsDeleted = 0` (else `NotFound`) and `UpdatedAt` must equal `@UpdatedAt`, null-safe (else `Stale`); type must be one of the company's active types (`InvalidType`); newly added employees must be active non-customer staff of the company (`InvalidEmployee`); `@IsDefault = 1` on an inactive row → `InactiveDefault`; duplicate check on `IsDeleted = 0` (`Exist`); no POS clearing on other rows; keep `UserResponsible` on update; never touch `BinWarehouse`; clear other defaults in the same company only. Checks and write run in one transaction. Returns `Response` = `OK` or one of the codes above, which the API maps to the messages in [`api-contract.md`](./api-contract.md#messages) |
| `Sp_ChangeWareHouseStatus` | **New** | `@CompanyId, @WarehouseId, @IsActive, @UserId` | Set `IsActive`, `UpdatedBy/At`; on deactivate also `IsDefault = 0`; returns `Response` = `'OK'` or `'NotFound'` |
| `SP_GetAllMap_Company_LocationType` | Existing | `@CompanyId` | Company's location types |
| `SP_GetUserWarehouse` | Existing | — | Active staff for the employee picker (endpoint now requires a token) |
| `DeleteWareHouseAndBin` | Existing, **no longer called** | — | The legacy delete endpoint is blocked (LOC-Q8); the procedure stays in the database |

### Seed data

None. Permissions `Location.Create` / `Location.Edit` already exist.

### Indexes

No extra indexes.

## Central DB

Not touched.

## Data lifecycle

| State | `IsActive` | `IsDeleted` | Location screen | Other screens |
|---|:-:|:-:|---|---|
| Active | 1 | 0 | Shown, "Active" | Shown |
| Inactive | 0 | 0 | Shown, "Inactive" | Hidden (they filter `IsActive = 1`) |
| Deleted (old data only) | 0 | 1 | Hidden | Hidden |

- Deactivate keeps `UserWarehouseMapping` and `BinWarehouse`; reactivate restores the location with them.
- No new row is ever set to Deleted by this screen.

## Migration notes

- Additive: no schema change; SP changes are re-runnable.
- Pre-check (done on dev, 2026-10-28): 141 rows = 94 active + 47 deleted, **0** legacy `IsActive = 0, IsDeleted = 0` rows; no company with more than one default; 2 active POS outlets in total. QA / prod not checked (plan R1).
- After this change, every location becomes a POS outlet the next time it is saved (plan R9).
- Order: SQL scripts → API → UI.
