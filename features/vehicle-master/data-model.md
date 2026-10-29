# Data model — Vehicle Master

Vehicles live in the Tenant DB table `dbo.VehicleMaster`, one row per vehicle, scoped by `CompanyId`. Stock transfers record the vehicle used in `dbo.TransferVehicleRelation.VehicleId` (saved by `SP_SaveTransferVehicleRelation`; read by `SP_GetTransferDetailbyId` and `Sp_GetTransferExportReport`). **No table schema change.**

> SQL scripts: `RMS_API/ProductManagement.API/Database Scripts/Stored Procedure/` (new file, `CREATE OR ALTER`), run by the user in SSMS. Facts checked against `D:\RMS\Database` (scripted 2026-10-28), corrected 2026-10-29.

## Tenant DB

### Tables

| Table | Change | Purpose |
|---|---|---|
| `VehicleMaster` | Existing (behaviour only) | One vehicle |
| `TransferVehicleRelation` | Not touched | `VehicleId`, `DriverId` of past dispatches — history keeps whatever was used |

### Columns `VehicleMaster` relies on

| Column | Type | Notes |
|---|---|---|
| `VehicleId` | uniqueidentifier | PK; default `newsequentialid()` (the insert relies on it) |
| `RegNo` | nvarchar(50) NULL | Uppercase letters and digits only, 1–20 (Q2); unique per company among **all** rows (active and inactive) |
| `BrandName` | nvarchar(500) NULL | Car manufacturer name; trimmed, 1–100, characters per Q3 |
| `CompanyId` | bigint NULL | Company boundary on every read and write; **never changed by an update** |
| `IsActive` | bit NULL | `1` active, `0` inactive (`NULL` treated as active) |
| `CreatedOn`, `CreatedBy` | datetime, uniqueidentifier | Set on insert (`GETUTCDATE()`); never changed |
| `LastUpdatedOn`, `LastUpdatedBy` | datetime, uniqueidentifier | Set on insert, edit and status change (`GETUTCDATE()`). `ISNULL(LastUpdatedOn, ISNULL(CreatedOn, '19000101'))` is the edit token |

There is **no `IsDelete` column**: nothing is ever deleted; status is the only lifecycle.

### Stored procedures

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `SP_GetVechileList` | Altered | `@CompanyId, @VehicleId = NULL, @ActiveOnly BIT = 0` | All company vehicles (or one), `ORDER BY IsActive DESC, RegNo`; `@ActiveOnly = 1` → only active (dispatch). Adds `LastUpdatedOn` (edit token) to the result |
| `SP_ValidateRegNo` | Altered | `@RegNo, @CompanyId, @VehicleId` | Trimmed, case-insensitive compare among **all** company vehicles (today: active only), other ids → `RtrValue` |
| `SP_AddEditVehicle` | Altered | existing + `@LastUpdatedOn DATETIME = NULL`; `@IsActive` kept, **ignored** | Per-company lock; codes `NotFound`, `Stale`, `Exist`, `ok`; insert active; edit changes `RegNo`, `BrandName` only, **filtered by `CompanyId`** (today the update has no company filter and overwrites `CompanyId`); UTC; returns `VehicleId` + `Response` |
| `SP_ChangeVehicleStatus` | **New** | `@CompanyId, @VehicleId, @IsActive, @UserId` | Same lock; `NotFound`; sets `IsActive`, `LastUpdatedBy/On` |
| `SP_DeleteVehicle` | Unchanged, **no longer called** | — | Endpoint blocked |

Lock: `sp_getapplock` on `'Vehicle_' + CompanyId` inside a transaction (as Department).

## Central DB

Not touched.

## Data lifecycle

| State | `IsActive` | This screen | Inbound Requisition dispatch | Past transfers / reports |
|---|:-:|---|---|---|
| Active | 1 (or NULL) | Shown, "Active" | Offered | Shown |
| Inactive | 0 | Shown, "Inactive" | Not offered | Shown |

## Migration notes

- Additive; re-runnable; existing rows unchanged. `RegNo` values breaking Q2 stay until next edit.
- Read-only pre-check (plan step 0): duplicate `RegNo` per company (trimmed, upper, all rows); `RegNo` / `BrandName` breaking Q2 / Q3; rows with NULL `CompanyId`, `IsActive`, `LastUpdatedOn`.
- Order: SQL → API and UI together (edit saves without `lastUpdatedOn` are refused).
