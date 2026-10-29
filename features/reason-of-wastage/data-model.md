# Data model — Reason of Wastage

Reasons live in the Tenant DB table `dbo.WastageReasonMaster`, one row per reason, scoped by `CompanyId`. `Wastage.ReasonMasterId` points at a reason (FK `FK_Wastage_WastageReasonMaster`). Onboarding (`usp_CompanyOnboard_Save`) copies the active rows of `DefaultWastageReasonMaster` with `DefaultWastageReasonId` set. **No table schema change.**

> SQL scripts: `RMS_API/ProductManagement.API/Database Scripts/Stored Procedure/` (new file, `CREATE OR ALTER`), run by the user in SSMS. Facts checked against `D:\RMS\Database` (scripted 2026-10-28).

## Tenant DB

### Tables

| Table | Change | Purpose |
|---|---|---|
| `WastageReasonMaster` | Existing (behaviour only) | One reason |
| `Wastage` | Not touched | Keeps `ReasonMasterId` of past write-offs |
| `DefaultWastageReasonMaster` | Not touched | Onboarding seed |

### Columns `WastageReasonMaster` relies on

| Column | Type | Notes |
|---|---|---|
| `ReasonMasterId` | int identity | PK |
| `Description` | nvarchar(max) NULL | The reason text. Trimmed, required, allowed characters per Q2, **no length cap**; unique per company among `ISNULL(IsDelete,0) = 0`, case-insensitive |
| `CompanyId` | bigint NULL | Company boundary on every read and write |
| `IsActive` | bit NOT NULL | See lifecycle |
| `IsDelete` | bit NULL, default 0 | Treated as `ISNULL(IsDelete, 0)`; never set by this feature |
| `UpdatedBy`, `UpdatedOn` | uniqueidentifier, datetime NULL | Set on insert, rename and status change (`GETUTCDATE()`; older rows hold server local time). There is no `CreatedAt`, so the edit token is `ISNULL(UpdatedOn, '19000101')` — same expression on read and on compare, so a NULL row still has a token |
| `DefaultWastageReasonId` | int NULL | Onboarding link; NULL for reasons added here; not changed |

### Stored procedures

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `SP_GetWarehouseReason` | Altered | `@UserId, @OpenMode, @CompanyId, @SearchText = NULL, @PageNumber = 1, @PageSize = 10` (paging kept for compatibility, **ignored**) | `IsDelete = 0` rows of the company (`'Active'` → also `IsActive = 1`), trimmed `LIKE` search, `ORDER BY IsActive DESC, Description`; `TotalRecords` = count; no `OFFSET` / `FETCH` |
| `SP_GetWestageReasonById` | Altered | `@UserId, @CompanyId, @ReasonMasterId` | Adds `IsDelete = 0`; returns `UpdatedOn` token and `TotalRecords = 1` |
| `SP_ValidateWastageReason` | **New** | `@CompanyId, @Description, @ReasonMasterId` | `IsExist` 1/0 — trimmed compare among non-deleted, other ids |
| `SP_AddEditResonMaster` | Altered | existing + `@UpdatedOn DATETIME = NULL`; `@IsActive` kept, **ignored** | Per-company lock; codes `NotFound`, `Stale`, `Exist`, `OK`; insert active; edit renames only; UTC; returns `ReasonMasterId` + `Response` |
| `SP_WestageReasonActive` | Altered | `@UserId, @CompanyId, @ReasonMasterId, @IsActive` | Same lock; codes `NotFound`, `OK`; sets `IsActive`, `UpdatedBy/On` |
| `SP_DeleteWestageReasonById` | Unchanged, **no longer called** | — | Endpoint blocked |

Lock: `sp_getapplock` on `'WastageReason_' + CompanyId` inside a transaction (as Department).

## Central DB

Not touched.

## Data lifecycle

| State | `IsActive` | `IsDelete` | This screen | Wastage Entry | Wastage Report |
|---|:-:|:-:|---|---|---|
| Active | 1 | 0 | Shown | Offered | Shown |
| Inactive | 0 | 0 | Shown, "Inactive" | Not offered | Shown |
| Deleted (old data) | any | 1 | Hidden | Not offered | Shown on past lines (unchanged) |

## Migration notes

- Additive; re-runnable; existing rows unchanged. Existing texts breaking Q2 stay until next rename.
- Read-only pre-check (plan step 0): duplicate texts per company (trimmed, case-insensitive, non-deleted); texts breaking Q2; rows with NULL `UpdatedOn` / `IsDelete` / `CompanyId`.
- Order: SQL → API and UI together (edit saves without `UpdatedOn` are refused).
