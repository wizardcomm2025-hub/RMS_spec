# Data model — Menu Section

Sections live in the Tenant DB table `dbo.MenuSection`, scoped by `CompanyId`. Finished items point at a section through `StockItem.MenuSectionId`; the POS loads items by section ids (`Sp_GetProductsData*`). Onboarding copies `Meta_MenuSection` rows and sets `Meta_MenuSectionId`. **No table schema change.**

> Scripts: edit the object files in `RMS_API/DBSCRIPT/SP/` (`CREATE OR ALTER`), run by the user in SSMS. Facts checked against `DBSCRIPT/` (2026-10-29).

## Tenant DB

### Tables

| Table | Change | Purpose |
|---|---|---|
| `MenuSection` | Existing (behaviour only) | One section |
| `StockItem` | Read only | `MenuSectionId`, `IsActive`, `IsDeleted`, `CompanyId` — the "used by active recipes" check |
| `Meta_MenuSection` | Not touched | Onboarding seed |

### Columns `MenuSection` relies on

| Column | Type | Notes |
|---|---|---|
| `MenuSectionId` | int identity | PK |
| `CompanyId` | bigint NOT NULL | Company boundary on every read and write |
| `MenuSectionName` | nvarchar(max) NOT NULL | Trimmed, 1–100, characters per Q2; unique per company (active and inactive), case-insensitive |
| `IsActive` | bit NOT NULL | Only lifecycle (no `IsDelete` column) |
| `Meta_MenuSectionId` | int NULL | Onboarding link; never changed by this screen |
| `CreatedBy`, `CreatedAt` | — | Set on insert (`GETUTCDATE()`) |
| `UpdatedBy`, `UpdatedAt` | — | Set on rename and status change (`GETUTCDATE()`). `ISNULL(UpdatedAt, ISNULL(CreatedAt, '19000101'))` is the edit token |

### Stored procedures (files in `DBSCRIPT/SP/`)

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `SP_GetMenuSectionWithPagination` | Altered | `@CompanyId, @RecordFrom, @RecordTo, @MenuSectionName` (paging and search kept, **ignored**) | All company sections, `ORDER BY IsActive DESC, MenuSectionName`; adds `UpdatedAt` token |
| `SP_GetMenuSectionById` | Altered | `@MenuSectionId, @CompanyId = NULL` (new) | Company-scoped; active **or** inactive; adds `UpdatedAt` token |
| `SP_ValidateMenuName` | Altered | unchanged | Trimmed compare among active and inactive |
| `SP_AddEditMenuSection` | Altered | existing + `@UpdatedAt DATETIME = NULL`; `@IsActive`, `@Meta_MenuSectionId` kept, **ignored** | Per-company lock; codes `NotFound`, `Stale`, `Exist`, `ok`; insert active; rename only, filtered by `CompanyId`; UTC; returns `MenuSectionId` + `Response` |
| `SP_ChangeMenuSectionStatus` | **New** | `@CompanyId, @MenuSectionId, @IsActive, @UserId` | Same lock; `NotFound`, `HasActiveRecipes`; sets `IsActive`, `UpdatedBy/At` |
| `SP_GetMenuSection` | Unchanged | `@CompanyId` | Active sections for Recipe's dropdown |
| `SP_DeleteMenuSection` | Unchanged, **no longer called** | — | Endpoint blocked |

Lock: `sp_getapplock` on `'MenuSection_' + CompanyId` inside a transaction.

## Central DB

Not touched.

## Data lifecycle

| State | `IsActive` | This screen | Recipe dropdown (new choice) | Existing recipe | POS |
|---|:-:|---|---|---|---|
| Active | 1 | Shown | Offered | Shown | Shown |
| Inactive | 0 | Shown, "Inactive" | Not offered | Kept, "<name> (inactive)" | No active recipes can be in it (Q1) |

## Migration notes

- Additive; re-runnable; existing rows unchanged.
- Read-only pre-check: duplicate names per company (trimmed, case-insensitive, all rows); names breaking Q2; inactive sections that still have active recipes (they stay as they are; the rule applies on the next switch-off).
- Order: SQL → API and UI together.
