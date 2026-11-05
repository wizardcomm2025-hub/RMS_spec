# Data model — Wastage Report

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No column change.** Read-only, plus one index.

| Table | Use |
|---|---|
| `Wastage` | `IsActive = 1`, `StockItemId`, `WarehouseId`, `Qty`, `UOM`, `ReasonMasterId`, `CreatedBy`, `CreatedAt` |
| `WastageReasonMaster` | Reason text |
| `StockItem`, `InventoryType` | Name, alias, main unit, alternate unit, conversion, type; company filter |
| `UserWarehouseMapping`, `Warehouse` | The user's active, non-deleted locations (D1) |
| `User` | Name of the person who recorded it |
| `UDF/dbo.fn_GetItemUnitCost` | Unit cost per main unit (D6) |

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetWastageHistoryReport` (**new**) | `@CompanyId, @UserId, @FromDate, @ToDate, @LocationId, @InventoryTypeId, @ReasonId, @Search, @View (Entries / Item / Reason), @PageNumber, @PageSize (0 = all, for export)` → one shape for all views: `RowKey, RecordedAt, WarehouseName, StockItemId, ItemName, ItemAlias, InventoryTypeName, Reason, Qty, Uom, UnitCost, Value, RecordedBy, EntryCount, ItemCount, TotalCount, TotalEntries, TotalValue` (columns that don't apply to a view are NULL / 0) |
| `Tables/dbo.Wastage` | **New guarded index** `IX_Wastage_Warehouse_CreatedAt (WarehouseId, CreatedAt) INCLUDE (StockItemId, Qty, UOM, IsActive, ReasonMasterId, CreatedBy)` |
| `SP/dbo.SP_Get_Wastage_Rpt` | Unchanged; no longer called by the screen (D10) |

## Rules in the procedure
- **Visible**: `IsActive = 1`, the item is the company's, the location is one of the user's (`@LocationId` must be too).
- **Day**: `CreatedAt >= @FromDate AND CreatedAt < @ToDate + 1`.
- **Quantity**: row unit blank or the main unit → as is; alternate unit with conversion > 0 → `Qty / Conversion`, unit = main; otherwise as saved, `Value` NULL (D4).
- **By item**: one row per item; `Qty` sums the known-unit rows; `EntryCount` counts all its entries.
- **By reason**: one row per reason (NULL reason = "No reason"); `EntryCount`, `ItemCount` (distinct items), `Value`.
- **Totals**: `TotalCount` = rows in the view (for paging); `TotalEntries` and `TotalValue` cover all filtered entries, and are the same in every view. Value is rounded per entry, so totals equal the sum of the entries.
- **Order**: Entries by `CreatedAt` newest first; By item / By reason by value highest first, then name.
