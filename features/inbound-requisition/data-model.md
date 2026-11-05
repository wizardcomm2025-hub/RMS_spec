# Data model — Inbound Requisition

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No table schema change.**

| Table | Use |
|---|---|
| `Requisition`, `RequisitionDetail` | Approved requisitions (`ReqStatusId = 3`, `IsApproved = 1`); `ApprovedQty` per line (falls back to `RequisitionQty` when 0) in the line `UOM` |
| `Transfer` | One dispatch: `TransferNumber` (`TRF/<FY>/<n>`), `TransferDate`, `FromWarehouseId` (sender), `ToWarehouseId` (requester), `TransferStatusId` 2 In transit / 3 Received, `ReqId`, `CompanyId`, `CreatedBy/At`, `Note`, `SerialNo` |
| `TransferDetail` | Lines: `StockItemId`, `StockItemName`, `UOM`, `TransferQty`, `ReqQty`, `ReqUOM`, `ReceiveQty` 0, `YieldQty` 0, `Finalize` 0 |
| `TransferVehicleRelation` | Vehicle, driver, responsible person per transfer |
| `Stock` | Sender stock reduced (main unit; a legacy row in the alternate unit is reduced in that unit) |
| `VehicleMaster`, `DriverMaster`, `UserWarehouseMapping`, `StockItem` | Checks and units |

**Sent before** per requisition line = Σ `TransferQty` of active transfers with `ReqId` = the requisition, status 2 or 3, same item.

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetInboundRequisitionList` (**new**) | Paged; filters; items, left-to-send count, transfer counts |
| `SP/dbo.Sp_GetRequisitionForDispatch` (**new**) | Header + lines with approved / sent / remaining / stock at sender (line unit) |
| `SP/dbo.Sp_DispatchRequisition` (**new**) | Codes `ok / NotFound / NotApproved / BadDate / BadVehicle / BadDriver / NoLine / BadQty / BadItem / OverRemaining / OverStock`; locks `TRF_<CompanyId>` then `SCK_<sender>` |
| `SP/dbo.Sp_SaveTransferDetails`, `SP_GetReqDetailsForTransfer`, `Sp_GetRequistionListForTransfer` | Not called by the screens any more (D5) |
