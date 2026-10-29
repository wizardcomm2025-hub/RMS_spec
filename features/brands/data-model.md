# Data model — Brand and Sub Brand

Tenant DB tables `dbo.StockType` (brand) and `dbo.StockSubType` (sub-brand, `StockTypeId` → brand), both scoped by `CompanyId`. Raw materials point at them through `StockItem.StockTypeId` / `StockItem.StockSubTypeId`. **No table schema change.**

> Scripts: edit the object files in `RMS_API/DBSCRIPT/SP/` (`CREATE OR ALTER`), run by the user in SSMS. Facts checked against `DBSCRIPT/` (2026-10-29).

## Columns relied on

| Table | Column | Notes |
|---|---|---|
| `StockType` | `StockTypeId` uniqueidentifier PK, `StockTypeName` nvarchar(max), `CompanyId` bigint, `IsActive` bit, `IsDeleted` bit NULL, `IsShortShelfLife` bit NULL, `CreatedBy/At`, `UpdatedBy/At` | Name trimmed 1–100, unique per company (all rows). `IsDeleted` / `IsShortShelfLife` never changed by these screens. Token `ISNULL(UpdatedAt, CreatedAt)` |
| `StockSubType` | `StockSubTypeId` PK, `StockSubTypeName`, `StockTypeId`, `CompanyId`, `IsActive`, `IsDeleted` NULL, `CreatedBy/At`, `UpdatedBy/At` | Name unique per company **within its brand** (all rows). `CompanyId` never changed. Same token |
| `StockItem` | `StockTypeId`, `StockSubTypeId`, `CompanyId` int, `IsActive`, `IsDeleted` | "Active raw material" = `IsActive = 1 AND ISNULL(IsDeleted, 0) = 0` |

## Stored procedures (`DBSCRIPT/SP/`)

| Procedure | Change | Purpose |
|---|---|---|
| `SP_GetStockTypeWithPagination` | Altered (params kept, ignored) | All company brands, active first; `ActiveSubBrandCount`, `ActiveItemCount`, `UpdatedAt` |
| `SP_GetStockTypeDetailsById` | Altered `+ @CompanyId = NULL` | Company-scoped; active or inactive; `UpdatedAt` |
| `SP_ValidateStockTypeName` | Altered | Trimmed, active and inactive |
| `SP_AddEditStockTypeDetails` | Altered `+ @UpdatedAt` | Lock `'Brand_' + CompanyId`; codes `NotFound`, `Stale`, `Exist`; add active; rename only, company-scoped; UTC; returns `StockTypeId`, `Response` |
| `SP_ChangeStockTypeStatus` | **New** | `NotFound`, `HasActiveSubBrands`, `HasActiveItems` |
| `SP_GetStockSubTypeWithPagination` | Altered (params kept, ignored) | **All** company sub-brands (active and inactive, any brand state) with `StockTypeName`, `StockTypeIsActive`, `ActiveItemCount`, `UpdatedAt` |
| `SP_GetStockSubTypeDetailsById` | Altered `+ @CompanyId = NULL` | Company-scoped; any state; `UpdatedAt` |
| `SP_ValidateStockSubTypeName` | Altered | Trimmed, within the brand, active and inactive; keeps its `RtrValue` result |
| `SP_AddEditStockSubTypeDetails` | Altered `+ @UpdatedAt` | Same lock; codes `BrandNotFound`, `BrandInactive`, `NotFound`, `Stale`, `Exist`, `InUseCannotMove`; never writes `CompanyId` |
| `SP_ChangeStockSubTypeStatus` | **New** | `NotFound`, `HasActiveItems`, `BrandInactive` |
| `Sp_GetStockTypeDDl`, `Sp_GetStockSubTypeDDl`, `SP_GetAllStockType` | Unchanged | Active dropdowns (Raw Material, Sub Brand dialog) |
| `SP_DeleteStockType`, `SP_DeleteStockSubType`, `SP_AddEditStockType`, `SP_AddEditStockSubType`, `SP_GetStockTypeById`, `SP_GetStockSubTypeById` | Unchanged, **no longer called** | Endpoints refused |

Brand and sub-brand saves share the lock `'Brand_' + CompanyId` so a brand can't be switched off while a sub-brand is being added under it.

## Lifecycle

| | Brand page | Sub Brand page | Raw Material dropdowns |
|---|---|---|---|
| Active | Shown | Shown | Offered |
| Inactive | Shown "Inactive" | Shown "Inactive" (D3) | Not offered |

## Migration notes

- Additive, re-runnable. Pre-check: duplicate names (brands per company; sub-brands per brand); names breaking Q3; inactive brands with active sub-brands or raw materials; inactive sub-brands with active raw materials (left as they are).
