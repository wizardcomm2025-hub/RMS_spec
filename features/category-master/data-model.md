# Data model — Category Master

Categories live in the Tenant DB table `dbo.StockGroup`, one row per category, scoped by `CompanyId`. The same table also holds asset categories (`ClassificationId = 2`); this feature reads and writes **only `ClassificationId = 1`** rows. Items point at categories through `StockItem.StockGroupId` (main) and `StockItem.SubStockGroupId` (sub). Inventory types come from `InventoryType` (per company). **No table schema change.**

> SQL scripts: `RMS_API/ProductManagement.API/Database Scripts/Stored Procedure/` (new file, `CREATE OR ALTER`), run by the user in SSMS. Facts below were checked against `D:\RMS\Database` (scripted 2026-10-28).

## Tenant DB

### Tables

| Table | Change | Primary key | Purpose |
|---|---|---|---|
| `StockGroup` | Existing (behaviour of `IsActive` changes) | `StockGroupId` (uniqueidentifier) | One category |
| `InventoryType` | Existing, read only | `InventoryTypeId` | The company's inventory types (`CompanyId`, `IsActive`) |
| `StockItem` | Existing, read only | — | Items; `StockGroupId`, `SubStockGroupId`, `IsActive` used for the deactivate check |
| `Meta_PermissionGroup` | Data only (rename) | `PermissionGroupId` | `GroupName` "Item Category" → "Category Master" |
| `Meta_Menu` | Data only (rename) | `MenuId` | `MenuName` "Item Category" → "Category Master" (tenant copy) |

### Columns `StockGroup` relies on

| Column | Type | Notes |
|---|---|---|
| `StockGroupId` | uniqueidentifier | PK; `NEWID()` on insert |
| `CompanyId` | int | Company boundary; every read and write filters on the token's company |
| `StockGroupName` | nvarchar(500) | Category Name; trimmed, 1–100, allowed characters per Q2; unique per company + type among `IsDeleted = 0`, case-insensitive |
| `StockGroupAlias` | nvarchar(500) | Category Code; trimmed, upper-case, 1–10 letters/digits; unique per company + type among `IsDeleted = 0` |
| `ClassificationId` | tinyint | `1` = item category (this feature); `2` = asset category (never touched) |
| `InventoryTypeId` | bigint | Must be an active type of the company |
| `ParentStockGroupId` | uniqueidentifier | Empty GUID or NULL = main category; else an active main category of the same company, type and classification |
| `IsActive`, `IsDeleted` | bit | See lifecycle |
| `ImagePath`, `IsIntangible`, `ThirdPartyId`, `ParentId`, `StockTypeId`, `StockSubTypeId` | — | Not on the form; **kept unchanged on edit** (`ThirdPartyId` = "NA" and `ParentId` = 0 on insert, as today) |
| `CreatedBy`, `CreatedAt` | — | Set on insert; never changed |
| `UpdatedBy`, `UpdatedAt` | — | Set on every update and status change. `ISNULL(UpdatedAt, CreatedAt)` is the edit token |

- **Checked in the SP:** company + classification on every read and write; type belongs to the company; parent rules; two-level rule; name and code uniqueness; edit token; deactivate / activate rules.

### Stored procedures

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `SP_GetStockGroupList` | Altered | `@CompanyId` | `ClassificationId = 1 AND IsDeleted = 0` (was `IsActive = 1`); returns `IsActive`. Only caller: this screen |
| `SP_GetStockGroupById` | Altered | `@StockGroupId, @CompanyId` (**new**) | Adds `CompanyId`, `ClassificationId = 1`, `IsDeleted = 0` filters (was `IsActive = 1`, any company); returns `IsActive`, `UpdatedAt` token, real `InventoryTypeName` |
| `SP_ValidateStockGroupName` | Altered | `@StkGroupName, @CompanyId, @StkGroupId, @ClasificationId, @InventoryTypeId` (**new**, default 0) | Trimmed name, `IsDeleted = 0`, same type when `@InventoryTypeId > 0` |
| `SP_AddEditStockGroup` | Altered | existing + `@UpdatedAt DATETIME = NULL` | Per-company lock; validation codes (below); insert or company-filtered update; keeps hidden columns |
| `SP_ChangeStockGroupStatus` | **New** | `@CompanyId, @StockGroupId, @IsActive, @UserId` | Activate / deactivate with the rules in [04](./04-switch-category-on-off.md) |
| `SP_GetStockGroupByInventoryType` | Unchanged | `@CompanyId, @InventoryTypeId` | Active main categories of a type (parent list; also used by Recipe) |
| `SP_GetInventoryTypeCompanyWise` | Unchanged | `@CompanyId` | Active inventory types |
| `SP_DelStockGroupById` | Unchanged, **no longer called** | — | Endpoint blocked |

`SP_AddEditStockGroup` result codes (`Response`): `OK`, `NotFound`, `Stale`, `InvalidType`, `InvalidParent`, `HasChildren`, `TypeLocked` (type change while any non-deleted sub-category or any `StockItem` — active or not — references it), `Exist` (name), `CodeExist`. Order of checks: not found → stale → type → parent → two-level → type lock → name → code.

`SP_ChangeStockGroupStatus` result codes: `OK`, `NotFound`, `HasActiveChildren`, `UsedByItems`, `ParentInactive`.

Both procedures take `sp_getapplock` on `'StockGroup_' + CompanyId` inside a transaction, so parallel saves can't create duplicates or break the two-level rule.

### Seed data

None. Permissions `create_item_category` / `edit_item_category` already exist.

### Indexes

No extra indexes.

## Central DB

- `Meta_Menu.MenuName` "Item Category" → "Category Master" (rename script, run by the user).

## Data lifecycle

| State | `IsActive` | `IsDeleted` | This screen | Other screens (item, recipe, stock) |
|---|:-:|:-:|---|---|
| Active | 1 | 0 | Shown, "Active" | Shown |
| Inactive | 0 | 0 | Shown, "Inactive" | Hidden (they filter `IsActive = 1`) |
| Deleted (old data only) | 0 | 1 | Hidden | Hidden |

## Migration notes

- Additive: no schema change; SP changes are re-runnable.
- **Pre-check before deploying** (plan step 1, read-only, user runs): per company — rows with `IsActive = 0, IsDeleted = 0` (will appear as Inactive); names breaking Q2; empty, lower-case, over-10 or duplicate codes per company + type; duplicate names per company + type; sub-categories whose parent is missing, inactive, of another type or itself a sub-category.
- Existing rows are not changed by the deployment (CAT-Q2).
- Rename script: updates `MenuName` / `GroupName` where the current value is "Item Category", in the Central DB and each Tenant DB; prints the affected rows; re-runnable.
- Order: SQL → API → UI (API and UI together: the API refuses edit saves without `UpdatedAt`).
