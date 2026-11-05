# Data model — Item Receive

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **No table schema change.**

| Table | Use |
|---|---|
| `Transfer` | `TransferStatusId` 2 In transit → 3 Received; `TransferStatus` (name from `TransferStatus`); `ReceiveBy`, `ReceiveAt` (receive date), `UpdatedBy/At`; `ReqId` → requisition number |
| `TransferDetail` | Per line: `TransferQty` (sent, line `UOM`), `ReceiveQty` (received, same unit), `ReceiveUOM` = `UOM`, `ReceiveNote` (shortage reason, D6), `ReceiveBy`, `ReceiveAt`; `YieldQty` / `Finalize` untouched |
| `TransferVehicleRelation`, `VehicleMaster`, `DriverMaster` | Vehicle / driver / contact shown on the list and the receive page |
| `RequisitionDetail` | Requested (approved) quantity per item |
| `Stock` | Receiver stock up (main unit; a legacy row in the alternate unit is increased in that unit; a missing row is created in the main unit) |
| `UserWarehouseMapping`, `Warehouse`, `StockItem` | Location check, names, units |

**Short** per line = `TransferQty − ReceiveQty` on a received transfer.

| Object | Change |
|---|---|
| `SP/dbo.Sp_GetItemReceiveList` (**new**) | Paged; filters; items, short count, vehicle / driver, received by / at |
| `SP/dbo.Sp_GetTransferForReceive` (**new**) | Header (repeated) + lines with requested / sent / received / note; `CanReceive` |
| `SP/dbo.Sp_ReceiveTransfer` (**new**) | Codes `ok / NotFound / AlreadyReceived / BadDate / FutureDate / BeforeTransfer / MissingLine / BadLine / BadQty / OverSent / NoReason`; lock `SCK_<receiver>`; `@RequireReason` (0 for old / mobile callers) |
| `SP/dbo.SP_GetTransferListForReceive`, `SP_GetTransferDetailForReceive`, `Sp_SaveReceiveDetailsV2`, `Sp_SaveReceiveDetails` | Not called by the screens any more; the two saves no longer called at all (D5) |
