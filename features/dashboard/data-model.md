# Data model — Dashboard

Read-only. **No table or column changes.** One data script adds a permission.

## Tables read
`Stock`, `StockItem`, `ItemWarehouseMap`, `Warehouse`, `UserWarehouseMapping`, `Wastage`, `Production`, `Requisition`, `RequisitionDetail`, `Transfer`, `TransferDetail`, `PurchaseOrderMaster`, `PurchaseOrderDetails`, `POHeadStatusLog`, `ReceiveHead`, `StockCheck`, `BillHead`; function `fn_GetItemUnitCost`.

## New procedures (`DBSCRIPT/SP/`)

| Procedure | Parameters | Returns |
|---|---|---|
| `Sp_GetDashboardAttention` | `@CompanyId, @UserId, @LocationId` | One row of counts: `ToApprove, ToDispatch, ToReceive, PoAwaitingDelivery, PoDraft, LocationsNotCounted` |
| `Sp_GetDashboardStock` | `@CompanyId, @UserId, @LocationId` | Result 1 (one row): `StockValue, ItemCount, LowCount, OutCount`. Result 2: top 8 lines to reorder: `StockItemId, ItemName, LocationName, Qty, Uom, ReorderLevel, Status` |
| `Sp_GetDashboardPeriod` | `@CompanyId, @UserId, @FromDate, @ToDate, @LocationId` | Result 1 (one row): `WastageValue, WastageEntries, ProductionCost, ProductionBatches`. Result 2: by day: `Day, WastageValue, ProductionCost`. Result 3: top 5 wasted: `StockItemId, ItemName, Qty, Uom, Value` |
| `Sp_GetDashboardPurchases` | `@CompanyId` | One row: `OpenPoCount, OpenPoValue` (submitted, not fully received) |
| `Sp_GetDashboardSales` | `@CompanyId, @UserId, @FromDate, @ToDate, @LocationId` | Result 1 (one row): `SalesTotal, BillCount, UnpaidAmount`. Result 2 by day: `Day, SalesTotal` |

All use the shared rule: user's active, non-deleted locations of the company; `@LocationId` NULL / 0 = all of them. A location outside that set gives empty figures.

## Definitions (D1, D2)
- **Stock**: as `Sp_GetStockReport` (stock rows + mapped items at 0, alternate unit ÷ conversion). An item is counted **once per item** for `ItemCount`, `LowCount` and `OutCount`, using the same status per location line as that report: an item is Out or Low if any of its location lines is.
- **Wastage value**: as `Sp_GetWastageHistoryReport` (main unit × today's `fn_GetItemUnitCost`, rounded per entry; active rows; `CreatedAt` day).
- **Production cost**: `SUM(FinalProductionCost)`, active rows by `ProductionDate`.
- **Open POs**: `StatusCode = Submitted`, receipt state not Full, as `Sp_GetPurchaseOrderList`; value = `Total`.
- **Not counted today**: my location with no active `StockCheck` where `CAST(StockCheckDate AS DATE)` = today.
- **Sales**: `BillHead` by `BillDate`, `OutletId` in my locations; `SalesTotal = SUM(BillAmountInclTax)`; `UnpaidAmount` = the same where `ISNULL(IsPaid, 0) = 0`.

## Data script
`RMS_API/specs/dashboard/add_dashboard_sales_permission.sql` (re-runnable): adds `view_dashboard_sales` to `Meta_Permission` and an active `RolePermission` for each company's **Administrator** role. Other roles get it from Role settings. `usp_CompanyOnboard_Save` already gives new companies every active `Meta_Permission`.
