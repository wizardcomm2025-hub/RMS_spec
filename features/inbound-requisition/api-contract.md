# API contract — Inbound Requisition

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetInboundRequisitionList` (**new**) | `viewlist_inbound_requisition` | `{ pageNumber, pageSize, fromDate, toDate, toWarehouseId, fromWarehouseId, show, search }` → rows (`requisitionId, requisitionNumber, requisitionDate, fromWarehouseName, toWarehouseName, itemCount, openItemCount, approvedByName, inTransitCount, receiveCount`) + `totalCount` |
| `/Setting/GetRequisitionForDispatch` (**new**) | `viewdetails_inbound_requisition` or `create_item_transfer` | `{ requisitionId }` → header + lines (`stockItemId, itemName, uom, mainUom, altUom, conversion, approvedQty, sentQty, remainingQty, stockQty`) |
| `/Setting/DispatchRequisition` (**new**) | `create_item_transfer` | `{ requisitionId, transferDate, vehicleId, driverId, personName, personPhone, note, lines: [{ stockItemId, qty }] }` → `transferId, transferNumber` |
| `/Setting/SaveTransferDetailsV2` | `create_item_transfer` | Kept; mapped to `DispatchRequisition` (vehicle / driver optional, direction from the requisition) |
| `/Setting/GetYourWarehouseTransfer`, `GetRequistionListForTransfer`, `GetReqDetailsForTransfer` | `viewlist_inbound_requisition` | Kept; `[CompanyAuthorize]` |

Messages (`status: false`): as in [02](./02-dispatch.md), plus `Something went wrong. Please try again.`. 401 for no / invalid / SuperAdmin token. Success: "Transfer {number} dispatched".
