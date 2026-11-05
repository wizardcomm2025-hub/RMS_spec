# Data model — PO Receive

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. Guarded table changes (re-runnable).

| Table | Use |
|---|---|
| `ReceiveHead` | One delivery: `POId`, `VendorId` (= PO vendor), `WarehouseId` (required for new saves), `InvoiceNo`, `ChallanNo`, attachments, `PurchaseValueExclTax` (Σ receiving × cost), `Tax1` (Σ line tax), `Tax2`/`Tax3` (0, kept), `PurchaseDiscountAmount`, `PurchaseFinalAmount` (receive total, editable), `ReceivedDate`, `IsDraft`, `IsActive`, `CompanyId`. **New columns:** `ReceiveNumber nvarchar(30) NULL`, `UpdatedAt datetime NULL`, `UpdatedBy uniqueidentifier NULL`. Edit token `ISNULL(UpdatedAt, CreatedAt)` |
| `PoReceivedDetails` | Lines: `ReceivedQty`, `DamageQty`, `UOM`, `Cost`, `Tax`, `FinalPrice` (line amount), `ReferencePoQty`, `Note`, `ChallanNo`. **New columns:** `PoDetailsId uniqueidentifier NULL` (PO line link), `StockQty decimal(18,4) NULL` (qty added to stock, main unit) |
| `ReceiveHeadStatusLog` / `ReceiveStatusMaster` | Written as today (history); the screen's status comes from `IsDraft` |
| `Stock` | `Qty` per item + location + company, main unit. Posting adds `StockQty` |
| `Item_Price_History` | One row per item per posting (price per unit in the received unit, as today) |
| `StockItem` | `UOM` (main), `AltUnit`, `Conversion` (alt units in 1 main unit) |
| `PurchaseOrderMaster` / `PurchaseOrderDetails` | Source PO; `Status = 'Submit'` and active |

**Received so far** for a PO line = Σ `ReceivedQty` of active **posted** lines with that `PoDetailsId`; legacy lines without `PoDetailsId` count by same PO + `StockItemId`. Compared in the PO line's unit.

**Receive number** (D1): `GRN/<FY>/<0001>`, FY from the received date (Apr–Mar), next = max suffix for the company + 1, under `sp_getapplock` `GRN_<CompanyId>`. Legacy receives keep NULL and show "—".

| Object | Change |
|---|---|
| `Tables/dbo.ReceiveHead` | Guarded `ADD ReceiveNumber, UpdatedAt, UpdatedBy` |
| `Tables/dbo.PoReceivedDetails` | Guarded `ADD PoDetailsId, StockQty` |
| `UDF/dbo.fn_PoLineReceived` (**new**, inline TVF) | `(@CompanyId, @PoId, @ExcludeReceiveHeadId)` → `PoDetailsId, ReceivedQty` posted so far |
| `SP/dbo.Sp_GetPoReceiveList` (**new**) | Paged list with filters and `TotalCount` |
| `SP/dbo.Sp_GetReceivablePoList` (**new**) | Company's submitted POs with something left; search |
| `SP/dbo.Sp_GetPoForReceive` (**new**) | PO header + lines with ordered / received / remaining and the item's units |
| `SP/dbo.SP_SaveRMSPOReceive` | Rewritten (`CREATE OR ALTER`): codes, company + permission-safe, PO by id, draft-only edits, validation, number, posting with conversion and over-receipt check, one transaction + lock, edit token. Returns `Response, ReceiveHeadId, ReceiveNumber, UpdatedAt` |
| `SP/dbo.SP_GetPOReceiveById` | Creator filter removed; + `ReceiveNumber`, `UpdatedAt`, `PoDetailsId`, `ChallanNo`; active lines only |
| `SP/dbo.Sp_DeletePoReceive` (**new**) | Draft only; codes `ok / NotFound / Locked` |
| `SP/dbo.Sp_GetPurchaseOrderList` | + `ReceiptState` (`None` / `Partial` / `Full`) |
| `SP/dbo.SP_GetPurchaseOrderReceiveByPONumber` | Creator filter removed (kept for old callers) |
