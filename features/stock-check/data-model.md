# Data model — Stock Check

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No table schema change.**

| Table | Use |
|---|---|
| `StockCheck` | One row per counted item: `StockCheckDate` (now), `StockCheckTime`, `StockItemId`, `Qty` + `UOM` (**main unit from now on**, D2), `WarehouseId`, `CompanyId`, `CreatedBy`, `Note`, `IsActive` |
| `Stock` | Per item + location + company: `Qty`, `UOM` (main), `TallyUpdate` |
| `StockItem` | `UOM` (main), `AltUnit`, `Conversion` (alt units in 1 main unit), `InvventoryTypeId`, `StockGroupId`, `IsActive` |
| `ItemWarehouseMap` | Items used at a location |
| `UserWarehouseMapping` | Locations a user can work in |

**System stock** shown = `Stock.Qty` converted to the main unit (a legacy row stored in the alternate unit is divided by `Conversion`; any other unit shows "—" and no variance).

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetStockCheckSheet` (**new**) | `(@CompanyId, @UserId, @WarehouseId, @InventoryTypeId)` → one row per item: id, name, group, main / alt unit, conversion, system qty (main), last count qty (main), last count at, last count by. Empty when the location isn't the user's |
| `SP/dbo.Sp_SaveStockCounts` (**new**) | `(@CompanyId, @UserId, @WarehouseId, @Lines XML)`; codes `ok / BadLocation / NoLine / BadQty / BadItem / BadUnit`; returns `Response, Saved, DetailItem, DetailValue`. One transaction, `sp_getapplock SCK_<WarehouseId>` |
| `SP/dbo.SP_AddEditStockCheck`, `SP/dbo.SP_SaveStockCheckIndvNew` | Not called by the API any more (kept in the database; D3) |
