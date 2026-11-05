# Data model — Stock Report

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No table schema change.** Read-only.

| Table | Use |
|---|---|
| `Stock` | Active rows (`ISNULL(IsActive, 1) = 1`) per item + location; quantity converted to the main unit (row in `StockItem.AltUnit` ÷ `Conversion`; row in the main unit or blank as is; other units ignored) |
| `ItemWarehouseMap` | Active mappings add the item to the location with quantity 0 when there is no stock row (Q2) |
| `StockItem`, `InventoryType` | Name, alias, main unit, reorder level, type; company filter |
| `UserWarehouseMapping`, `Warehouse` | The user's active, non-deleted locations (Q1) |
| `UDF/dbo.fn_GetItemUnitCost` | Unit cost per main unit (Q3) |

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetStockReport` (**new**) | `@CompanyId, @UserId, @LocationId, @InventoryTypeId, @Show (All/Low/Out), @Search, @PageNumber, @PageSize (0 = all, for export)` → one row per item × location for the page's items: item, alias, type, unit, reorder level, location, quantity, unit cost, value, status; plus `TotalCount` (items) and `TotalValue` (all filtered) |
| `SP/dbo.SP_GetTotQtyStockItemWiseRpt_Page`, `SP_GetStoQtyWarehouseWiseRpt`, `SP_GetStockExportReport` | No longer called by the screen (D5) |
