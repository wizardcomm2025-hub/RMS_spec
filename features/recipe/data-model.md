# Data model — Recipe

Tenant DB. Scripts: object files in `RMS_API/DBSCRIPT/` (`CREATE OR ALTER`, `IF NOT EXISTS`).

| Table | Use |
|---|---|
| `StockItem` | The recipe (`ClassificationId = 1`, `InvventoryTypeId` = a non-raw type of the company). `RecipeQty` / `RecipeUom` batch; `YieldReport` %; `PurchaseCost` effective cost per recipe unit; `SalesPrice`; `MenuSectionId`; `PureVeg`; `SubStockGroupId`. **New column `IsManualCost bit NOT NULL DEFAULT 0`**. States as Raw Material; edit token `ISNULL(UpdatedAt, CreatedAt)` |
| `ReceipeDetail` | Ingredient lines: `ParentStockId` = recipe, `StockItemId` = ingredient, `Quantity` + `UOM` as entered, `PrimaryQty` in the ingredient's main unit, `YieldQty`, `UsedQty`, `WastageQty`, `IsActive` |
| `ItemWarehouseMap`, `Stock`, `StockCheck` | Locations and zero opening rows (as Raw Material) |
| `Item_Map_POS`, `RecipeImage` | POS item mapping and primary image (unchanged behaviour) |

**Loop** = the ingredient is the recipe itself, or the recipe is reachable from the ingredient through active `ReceipeDetail` rows (recursive CTE, max depth 32).

**"Used"** (delete, unit lock) = `dbo.fn_IsStockItemUsed` (Raw Material) **extended** with: active `Item_Map_POS` row, any `OrderDetails` / `KOT` row with the item. (Recipe's own ingredient lines are not "use".)

| Object | Change |
|---|---|
| `Tables/dbo.StockItem` | Guarded `ALTER TABLE … ADD IsManualCost` appended to the file — on an existing database run **only that block** |
| `UDF/dbo.fn_IsStockItemUsed` | + POS mapping, orders, KOTs |
| `UDF/dbo.fn_GetItemUnitCost` (**new**) | Cost per main / recipe unit of any item: raw → last purchase cost (pre-tax, per main unit) else standard; recipe → manual cost when `IsManualCost`, else Σ(line `PrimaryQty` × `fn_GetItemUnitCost`(ingredient)) ÷ (batch × yield) — recursive |
| `SP_GetRecipeListWithPagination` | `@IsActive`, `@StockGroupId`, `@MenuSectionId` filters; active + inactive, never deleted; `IsActive`, `CanDelete`, `UnitCost`, `IsManualCost`, `TotalCount` |
| `SP_GetStockItemByIdForRecipe` | `@CompanyId`; `IsActive`, `UpdatedAt`, `IsUsed`, `IsManualCost`, `CalculatedCost` |
| `SP_GetRecipeListById` | `@CompanyId`; per line `UnitCost` (ingredient cost per main unit) |
| `Sp_GetStockItemSuggestionforRecipe` | `@ExcludeId` (the recipe being edited); active, not deleted; no duplicates; `UnitCost` |
| `SP_AddEditRecipeItem` | Rewritten: codes (incl. `TypeLocked` — type fixed on edit, Q6); decimals; company-scoped; lines diffed in one transaction; ingredient / loop / company checks; stock rows of other locations untouched; `@IsManualCost`, `@UpdatedAt`; effective cost stored |
| `SP_DelRecipe` | `@CompanyId` (line must belong to a company recipe); no longer called by the dialog (lines are removed by the save) |
| `Sp_ChangeRecipeStatus` (**new**), `Sp_DeleteRecipe` (**new**) | Status; delete-if-unused |
