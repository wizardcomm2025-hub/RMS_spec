# Data model — Stock Check Report

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No column change.** Read-only, plus one index.

| Table | Use |
|---|---|
| `StockCheck` | The counts: `IsActive = 1`, `StockCheckDate`, `StockCheckTime`, `StockItemId`, `Qty`, `UOM`, `WarehouseId`, `CreatedBy`, `CreatedAt`, `Note` |
| `StockItem`, `InventoryType` | Name, alias, main unit, alternate unit, conversion, type, `CreatedAt` (D6); company filter |
| `UserWarehouseMapping`, `Warehouse` | The user's active, non-deleted locations (D1) |
| `User` | Name of the person who counted (`CreatedBy`) |
| `UDF/dbo.fn_GetItemUnitCost` | Unit cost per main unit (D5) |

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetStockCheckReport` (**new**) | `@CompanyId, @UserId, @FromDate, @ToDate, @LocationId, @InventoryTypeId, @Search, @PageNumber, @PageSize (0 = all, for export)` → one row per count: `StockCheckId, CountedAt, WarehouseId, WarehouseName, StockItemId, ItemName, ItemAlias, InventoryTypeName, Qty, Uom, UnitCost, Value (NULL for an unknown unit), CountedBy, Note, TotalCount, TotalValue` |
| `Tables/dbo.StockCheck` | **New guarded index** `IX_StockCheck_Warehouse_Date (WarehouseId, StockCheckDate) INCLUDE (StockItemId, Qty, UOM, IsActive, CreatedBy, CreatedAt, StockCheckTime)` |
| `SP/dbo.SP_GetStockCheckReportData`, `UDF/dbo.fun_Get_Min_Max_Stock_*` | Unchanged; no longer called by the screen (D9) |

## Rules in the procedure
- **Day**: `StockCheckDate >= @FromDate AND StockCheckDate < DATEADD(day, 1, @ToDate)` (D3); `CountedAt` = the date part of `StockCheckDate` + `ISNULL(StockCheckTime, CAST(CreatedAt AS TIME))`.
- **Quantity**: row unit blank or the main unit → as is; alternate unit with conversion > 0 → `Qty / Conversion`, unit = main; otherwise as saved, `Value` NULL (D4).
- **Hidden**: `Qty = 0 AND CreatedAt = StockItem.CreatedAt` (D6).
- **Totals**: `TotalCount` and `TotalValue` cover all filtered rows, not the page.
- **Order**: `CountedAt` newest first, then item name.
