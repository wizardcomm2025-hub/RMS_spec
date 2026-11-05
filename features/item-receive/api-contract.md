# API contract — Item Receive

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetItemReceiveList` (**new**) | `viewlist_item_receive` | `{ pageNumber, pageSize, fromDate, toDate, toWarehouseId, fromWarehouseId, show: "InTransit" \| "Received" \| "", search }` → rows (`transferId, transferNumber, transferDate, fromWarehouseName, toWarehouseName, requisitionNumber, itemCount, shortCount, vehicleRegNo, driverName, statusId, statusName, receivedAt, receivedByName`) + `totalCount` |
| `/Setting/GetTransferForReceive` (**new**) | `viewlist_item_receive` or `receive_item_receive` | `{ transferId }` → header (`transferNumber, transferDate, fromWarehouseName, toWarehouseName, requisitionNumber, vehicleRegNo, driverName, personName, personPhone, note, statusId, receivedAt, receivedByName, canReceive`) + lines (`transferDetailId, stockItemId, itemName, uom, requestedQty, sentQty, receivedQty, note`) |
| `/Setting/ReceiveTransfer` (**new**) | `receive_item_receive` | `{ transferId, receiveDate (yyyy-MM-dd), lines: [{ transferDetailId, qty, note }] }` → `transferNumber` |
| `/Setting/GetTransferListForReceive`, `GetTransferDetailForReceive` | `viewlist_item_receive` | Kept; `[CompanyAuthorize]`; company from the token |
| `/Setting/SaveReceiveDetailsV2` | `receive_item_receive` | Kept; mapped to `ReceiveTransfer` (reason not forced, lines not sent = 0) |
| `/Setting/GetReceiveList`, `GetTransferDetailsForRec` (mobile) | `viewlist_item_receive` | `[CompanyAuthorize]`; `UserId` / `CompanyId` from the token, body values ignored; detail only for a transfer to one of the user's locations |
| `/Setting/SaveReceivedData` (mobile) | `receive_item_receive` | `[CompanyAuthorize]`; mapped to `ReceiveTransfer` (reason not forced) |

Messages (`status: false`): as in [02](./02-receive.md). 401 for no / invalid / SuperAdmin token. Success: "Transfer {number} received".
