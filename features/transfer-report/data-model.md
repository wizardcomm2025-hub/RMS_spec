# Data model — Transfer Report

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No column change.** Read-only, plus one index.

| Table | Use |
|---|---|
| `Transfer` | `IsActive = 1`, `CompanyId`, `TransferNumber`, `TransferDate`, `FromWarehouseId`, `ToWarehouseId`, `TransferStatusId` (2 In transit, 3 Received), `CreatedBy`, `ReceiveBy`, `ReceiveAt`, `ReqId` |
| `TransferDetail` | Active lines: `StockItemId`, `StockItemName`, `TransferQty`, `ReceiveQty`, `UOM`, `ReceiveNote`, `ReceiveBy` |
| `TransferStatus` | Status name |
| `Requisition` | Requisition number |
| `Warehouse`, `UserWarehouseMapping` | Location names; the user's active, non-deleted locations (Q2) |
| `StockItem`, `User` | Item name, alias, units, conversion; names of the people |
| `UDF/dbo.fn_GetItemUnitCost` | Unit cost per main unit (D7) |

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetStockTransferReport` (**new**) | `@CompanyId, @UserId, @FromDate, @ToDate, @LocationId, @Show (All / InTransit / Received / Short), @Search, @PageNumber, @PageSize (0 = all, for export)` → one row per line of the page's transfers: transfer fields (`TransferId, TransferNumber, RequisitionNumber, TransferDate, FromWarehouseName, ToWarehouseName, SentBy, ReceivedBy, ReceivedAt, StatusId, StatusName`), line fields (`TransferDetailId, StockItemId, ItemName, ItemAlias, Uom, SentQty, ReceivedQty, ShortQty, UnitCost, SentValue, ShortValue, ReceiveNote`), totals (`TotalCount, TotalSentValue, TotalShortValue`) |
| `Tables/dbo.Transfer` | **New guarded index** `IX_Transfer_Company_Date (CompanyId, TransferDate) INCLUDE (FromWarehouseId, ToWarehouseId, TransferStatusId, IsActive)` |
| `SP/dbo.Sp_GetTransferReport`, `Sp_GetTransferExportReport` | Unchanged; no longer called by the screen (D11) |

## Rules in the procedure
- **Visible**: `T.CompanyId = @CompanyId`, `T.IsActive = 1`, at least one active line, and `From` or `To` is one of the user's locations; `@LocationId` must also be one of them and matches either end.
- **Day**: `TransferDate >= @FromDate AND TransferDate < @ToDate + 1`.
- **Received** = `TransferStatusId = 3`. Before that, `ReceivedQty`, `ShortQty`, `ShortValue`, `ReceivedBy` and `ReceivedAt` are NULL (D4, D8).
- **Short** = `TransferQty − ReceiveQty`, signed; *With shortage* = received with a line where Short > 0; `ShortValue` only when Short > 0 (D5).
- **Value** = main-unit quantity × unit cost; unit blank or main as is, alternate ÷ conversion, otherwise NULL (D6).
- **Search** matches the transfer number, requisition number, or any active line's item name / alias, and returns the whole transfer (D3).
- **Totals** cover every filtered transfer. **Order**: `TransferDate` newest first, then `CreatedAt`; lines by item name. Paging is by transfer.
