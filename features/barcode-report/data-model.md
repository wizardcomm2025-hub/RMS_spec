# Data model — Barcode Report

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No column change.** Read-only, plus one index.

| Table | Use |
|---|---|
| `Barcode` | `IsActive = 1`, `BarcodeNumber`, `StockItemId`, `UOM`, `InitialQty`, `RemainingQty`, `DistributorId`, `MfgDate`, `ExpDate`, `LocationId` |
| `StockItem`, `InventoryType` | Name, alias, main unit, alternate unit, conversion, type; company filter; active, not deleted (D7) |
| `Distributor` | Vendor name |
| `UserWarehouseMapping`, `Warehouse` | The user's active, non-deleted locations; location name (D2) |
| `UDF/dbo.fn_GetItemUnitCost` | Unit cost per main unit (D5) |

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetBarcodeStockReport` (**new**) | `@CompanyId, @UserId, @LocationId, @InventoryTypeId, @Show (InStock / Expiring / Expired / UsedUp), @Search, @PageNumber, @PageSize (0 = all, for export)` → one row per barcode of the page's items: item (`StockItemId, ItemName, ItemAlias, InventoryTypeName, MainUom, ItemQty, ItemValue, BarcodeCount, NearestExpiry`), barcode (`BarcodeId, BarcodeNumber, WarehouseName, VendorName, MfgDate, ExpDate, Status, InitialQty, RemainingQty, Uom, UnitCost, Value`), totals (`TotalCount` items, `TotalBarcodes`, `TotalValue`) |
| `Tables/dbo.Barcode` | **New guarded index** `IX_Barcode_StockItem (StockItemId) INCLUDE (LocationId, RemainingQty, ExpDate, IsActive, UOM)` |
| `SP/dbo.SP_GetTotalQtyOfBarcodeReport`, `SP_GetBarcodeReportByItemId`, `SP_GetBarcodeExportReport` | Unchanged; no longer called by the screen (D10) |

## Rules in the procedure
- **Visible**: active barcode; the company's active, non-deleted item; `LocationId` one of the user's, or NULL when no location is chosen (D2).
- **Status** (`Status` column, by `CAST(GETDATE() AS DATE)`): `UsedUp`, `Expired`, `Expiring`, or `''` (D1). `@Show` picks the barcodes; `InStock` = remaining > 0.
- **Search**: a barcode matches if its number matches, or its item's name or alias matches (D3).
- **Quantity**: barcode unit blank or the main unit → as is; alternate unit with conversion > 0 → `÷ Conversion` for the item's total and value; otherwise left out of both, `Value` NULL (D4). A used-up barcode's value is 0.
- **Item figures** (`ItemQty`, `ItemValue`, `BarcodeCount`, `NearestExpiry`) cover only the barcodes that matched; `NearestExpiry` is the earliest `ExpDate` among them with remaining > 0.
- **Totals**: `TotalCount` = items, `TotalBarcodes` and `TotalValue` cover all that matched, not the page; value is rounded per barcode, so totals equal the sum of the export.
- **Order**: D6. Paging is by item.
- Dates are returned as dates, never text (B1).
