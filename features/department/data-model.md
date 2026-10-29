# Data model — Department

Departments live in the Tenant DB table `dbo.DepartmentMaster`, one row per department, scoped by `CompanyId`. Users point at a department through `User.DepartmentId`; roster patterns and rosters through `DepartmentmasterId`; requisitions, receiving, assets, barcodes and checklists also keep department ids. **No table schema change.**

> SQL scripts: `RMS_API/ProductManagement.API/Database Scripts/Stored Procedure/` (new file, `CREATE OR ALTER`), run by the user in SSMS. Facts checked against `D:\RMS\Database` (scripted 2026-10-28).

## Tenant DB

### Tables

| Table | Change | Purpose |
|---|---|---|
| `DepartmentMaster` | Existing (behaviour only) | One department |
| `User` | Read only | `DepartmentId`, `IsActive` — deactivate check |
| `RosterPattern` | Read only | `DepartmentmasterId`, `IsActive` — deactivate check |

### Columns `DepartmentMaster` relies on

| Column | Type | Notes |
|---|---|---|
| `DepartmentMasterId` | uniqueidentifier | PK; `NEWID()` on insert |
| `DepartmentName` | nvarchar(max) | Trimmed, 1–100, allowed characters per Q2; unique per company among `IsDelete = 0`, case-insensitive |
| `CompanyId` | bigint | Company boundary on every read and write |
| `IsActive`, `IsDelete` | bit | See lifecycle |
| `CreatedBy`, `CreatedAt` | — | Set on insert (`GETUTCDATE()`); never changed |
| `UpdatedBy`, `UpdatedAt` | — | Set on every rename and status change (`GETUTCDATE()`). `ISNULL(UpdatedAt, CreatedAt)` is the edit token |

### Stored procedures

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `SP_GetAllDepartment` | Altered | `@CompanyId, @SearchName = '', @PageNumber = 1, @PageSize = 10` (kept for compatibility, **ignored**) | All `IsDelete = 0` rows of the company, `ORDER BY IsActive DESC, DepartmentName`; `TotalRecord` kept; no `OFFSET` / `FETCH` |
| `SP_GetDepartmentById` | Altered | `@DepartmentMasterId, @CompanyId` | Adds `IsDelete = 0`; returns `ISNULL(UpdatedAt, CreatedAt) AS UpdatedAt` |
| `SP_ValidateDeptName` | Altered | `@DeptName, @CompanyId, @DeptMasterId` | Trimmed compare among `IsDelete = 0` (active and inactive) |
| `SP_AddEditDepartment` | Altered | existing + `@UpdatedAt DATETIME = NULL` | Per-company lock; codes `NotFound`, `Stale`, `Exist`; insert active; rename only on edit (keeps `IsActive`); UTC dates; returns `DepartmentMasterId` + `Response` |
| `SP_ChangeDepartmentStatus` | **New** | `@CompanyId, @DepartmentMasterId, @IsActive, @UserId` | Same lock; codes `NotFound`, `HasActiveUsers`, `HasActiveRosterPatterns`; sets `IsActive`, `UpdatedBy/At` |
| `SP_DeleteDepartment` | Unchanged, **no longer called** | — | Endpoint blocked |
| `SP_GetDepartmentDDl` | Unchanged | `@companyId` | Active departments for dropdowns (User List form) |

Lock: `sp_getapplock` on `'Department_' + CompanyId` inside a transaction (as Location / Category Master).

## Central DB

Not touched.

## Data lifecycle

| State | `IsActive` | `IsDelete` | This screen | Dropdowns (User form…) | Roster filter |
|---|:-:|:-:|---|---|---|
| Active | 1 | 0 | Shown | Shown | Shown |
| Inactive | 0 | 0 | Shown, "Inactive" | Hidden | Shown |
| Deleted (old data) | 0 | 1 | Hidden | Hidden | Hidden |

## Migration notes

- Additive; re-runnable; existing rows unchanged. Existing names breaking Q2 stay until next rename.
- Read-only pre-check (plan step 0): duplicate names per company (trimmed, case-insensitive) including inactive; names breaking Q2; per company counts; departments with active users / roster patterns.
- Order: SQL → API and UI together (edit saves without `UpdatedAt` are refused; the UI switch moves to the new endpoint).
