# Data model — Production

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No table schema change.**

| Table | Use |
|---|---|
| `Production` | Header: `ItemId` (recipe item), `LocationId`, `ProductionDate` (date picked, D3), `Qty` (recipe item main unit), `TotalStockCost` (Σ line cost), `TotalAdjustmentCost`, `OverheadCost`, `FinalProductionCost`, `CompanyId`, `CreatedBy`, `CreatedAt` (local time), `IsActive` 1 |
| `ProductionDetail` | Per ingredient: `ItemId`, `UOM` = `PrimaryUOM` = ingredient main unit, `StandardYield` = standard quantity, `ActualYield` = `TotalQty` = `PrimaryQty` = actual quantity, `ItemCost` = actual × unit cost, `AdjustmentCost`, `AdjustmentReason`, `Note` (unit cost used) |
| `StockItem`, `ReceipeDetail`, `InventoryType` | Producible items (D1); recipe lines (`PrimaryQty` per batch, ingredient main unit); batch `RecipeQty`, yield `YieldReport`; units / conversion |
| `Stock` | Ingredients down (main unit; a legacy row in the alternate unit is reduced in that unit), produced item up (row created in the main unit if missing) |
| `UserWarehouseMapping`, `Warehouse` | Location check |

Standard quantity per ingredient = `ReceipeDetail.PrimaryQty` × production qty ÷ (`RecipeQty` (0 → 1) × yield % (0 or > 100 → 100) ÷ 100).

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetProductionList` (**new**) | Paged; date range, location (my locations), search; costs, unit cost, by |
| `SP/dbo.Sp_GetProductionItems` (**new**) | Producible items of the company (search, top 500; the picker filters them): unit, batch, yield, ingredient count |
| `SP/dbo.Sp_GetProductionSheet` (**new**) | Recipe ingredients for item + location: main unit, per-batch qty, stock here (main unit), unit cost (`fn_GetItemUnitCost`); header batch / yield / unit |
| `SP/dbo.Sp_SaveProduction` (**new**) | Codes `ok / BadItem / BadLocation / BadDate / FutureDate / BadQty / BadOverhead / RecipeChanged / BadActual / NoReason / NegativeCost / Short`; lock `SCK_<location>`; `@UseStandard` for the old endpoint |
| `SP/dbo.Sp_GetProductionDetail` (**new**) | Header + lines for one production at one of the user's locations |
| `SP/dbo.SP_AddEditProduction`, `SP_GetAllProductionList`, `SP_GetProductionDetailById`, `Sp_GetRecipeItemDetails_IncludingProduction` | No longer called by the screens; `SP_AddEditProduction` not called at all (D8) |
