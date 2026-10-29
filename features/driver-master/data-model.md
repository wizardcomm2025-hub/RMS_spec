# Data model — Driver Master

Drivers live in the Tenant DB table `dbo.DriverMaster`, one row per driver, scoped by `CompanyId`. Past dispatches keep the driver in `dbo.TransferVehicleRelation.DriverId` (read by `SP_GetTransferDetailbyId`, `Sp_GetTransferExportReport`). **No table schema change.**

> SQL scripts: `RMS_API/ProductManagement.API/Database Scripts/Stored Procedure/` (new file, `CREATE OR ALTER`), run by the user in SSMS. Facts checked against `D:\RMS\Database` (scripted 2026-10-28).

## Tenant DB

### Tables

| Table | Change | Purpose |
|---|---|---|
| `DriverMaster` | Existing (behaviour only) | One driver |
| `TransferVehicleRelation` | Not touched | History |

### Columns `DriverMaster` relies on

| Column | Type | Notes |
|---|---|---|
| `DriverId` | uniqueidentifier | PK; default `newsequentialid()` |
| `DriverName` | nvarchar(max) NULL | Trimmed, 1–100, characters per Q3 |
| `LicNo` | nvarchar(50) NULL | Trimmed, uppercase, 1–50, characters per Q2; unique per company among all rows |
| `ProfileImage` | nvarchar(max) NULL | `data:image/jpeg;base64,…` or `data:image/png;base64,…`, ≤ 1 MB decoded; NULL / empty = no photo |
| `CompanyId` | bigint NULL | Company boundary; never changed by an update |
| `IsActive` | bit NULL | `1` active, `0` inactive (`NULL` treated as active) |
| `CreatedOn`, `CreatedBy` | datetime, uniqueidentifier | Set on insert (`GETUTCDATE()`) |
| `LastUpdatedOn`, `LastUpdatedBy` | datetime, uniqueidentifier | Set on insert, edit and status change (`GETUTCDATE()`; today an edit doesn't set them). `ISNULL(LastUpdatedOn, ISNULL(CreatedOn, '19000101'))` is the edit token |

No `IsDelete` column; status is the only lifecycle.

### Stored procedures

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `GetDriverList` | Altered | `@CompanyId, @DriverId = NULL, @ActiveOnly BIT = 0` | All company drivers (or one), `ORDER BY IsActive DESC, DriverName`; `@ActiveOnly = 1` → active only. Adds `LastUpdatedOn` |
| `SP_ValidateLicNo` | Altered | `@LicNo, @CompanyId, @DriverId` | Trimmed, case-insensitive compare among **all** company drivers (today active only), other ids → `RtrValue` |
| `SP_AddEditDriver` | Altered | existing + `@LastUpdatedOn DATETIME = NULL`, `@KeepImage BIT = 0`; `@IsActive` kept, **ignored** | Per-company lock; codes `NotFound`, `Stale`, `Exist`, `ok`; insert active; edit changes name, licence and (unless `@KeepImage = 1`) photo, **filtered by `CompanyId`**; UTC; returns `DriverId` + `Response` |
| `SP_ChangeDriverStatus` | **New** | `@CompanyId, @DriverId, @IsActive, @UserId` | Same lock; `NotFound`; sets `IsActive`, `LastUpdatedBy/On` |
| `SP_DeleteDriver` | Unchanged, **no longer called** | — | Endpoint blocked |

Lock: `sp_getapplock` on `'Driver_' + CompanyId` inside a transaction.

## Central DB

Not touched.

## Data lifecycle

| State | `IsActive` | This screen | Inbound Requisition dispatch | Past transfers / reports |
|---|:-:|---|---|---|
| Active | 1 (or NULL) | Shown, "Active" | Offered | Shown |
| Inactive | 0 | Shown, "Inactive" | Not offered | Shown |

## Migration notes

- Additive; re-runnable; existing rows unchanged. Names, licences and photos breaking Q2–Q4 stay until next edit; an edit that keeps the photo doesn't re-check it.
- Read-only pre-check (plan step 0): duplicate licences per company (trimmed, upper, all rows); values breaking Q2 / Q3; photos that aren't JPG / PNG data URLs or are over 1 MB; rows with NULL `CompanyId`, `IsActive`, `LastUpdatedOn`.
- Order: SQL → API and UI together.
