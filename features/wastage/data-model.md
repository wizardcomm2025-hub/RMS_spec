# Data model — Wastage

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No table schema change.**

| Table | Use |
|---|---|
| `Wastage` | One row per line: `CompanyId`, `StockItemId`, `WarehouseId`, `Qty` + `UOM` (**main unit from now on**, D2), `ReasonMasterId`, `BarCodeId`, `CreatedBy`, `CreatedAt` (server local time, as the report expects), `IsActive` (0 = cancelled), `UpdatedBy`, `UpdatedAt` |
| `Stock` | Reduced on save, restored on cancel; item + location |
| `Barcode` | `RemainingQty` reduced / restored; `CompanyId`, `StockItemId`, `LocationId`, `UOM`, `IsActive` |
| `WastageReasonMaster` | Reasons: `CompanyId`, `IsActive`, `IsDelete` |
| `StockItem`, `ItemWarehouseMap`, `UserWarehouseMapping` | Units / conversion; items at a location; user's locations |

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetWastageSheet` (**new**) | Items in stock (> 0) at the user's location: id, name, group, main / alt unit, conversion, stock qty (main) |
| `SP/dbo.Sp_FindWastageBarcode` (**new**) | Barcode by number: company, active, remaining > 0, location = the chosen one (or none) |
| `SP/dbo.Sp_SaveWastage` (**new**) | `(@CompanyId, @UserId, @WarehouseId, @Lines XML)`; codes `ok / BadLocation / NoLine / BadQty / BadItem / BadReason / BadUnit / OverStock / BadBarcode`; one transaction, `sp_getapplock SCK_<WarehouseId>` (same lock as Stock Check) |
| `SP/dbo.Sp_GetWastageToday` (**new**) | Today's active entries at the location |
| `SP/dbo.Sp_CancelWastage` (**new**) | Same-day cancel; codes `ok / NotFound / TooLate` |
| `SP/dbo.SP_Get_Wastage_Rpt`, `SP_GetTopWastageDashboard`, `SP_GetWidgetWastageByReason` | `+ IsActive = 1` on `Wastage` (D4) |
| `SP/dbo.SP_AddEditWastage`, `SP_Get_Stock_Wastage`, `SP_SearchWastageItemByBarCode` | Not called by the API any more (D5) |
