# API contract — PO Receive

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetPoReceiveList` (**new**) | `viewlist_receive_asset` | `{ pageNumber, pageSize, fromDate, toDate, status, vendorId, warehouseId, search }` → rows (`receiveHeadId, receiveNumber, receivedDate, poId, poNumber, vendorName, warehouseName, invoiceNo, challanNo, total, isDraft`) + `totalCount` |
| `/Setting/GetReceivablePurchaseOrders` (**new**) | `receive_receive_asset` | `{ search }` → `poId, poNumber, poDate, vendorId, vendorName` (max 50) |
| `/Setting/GetPoForReceive` (**new**) | `receive_receive_asset` | `{ poId, receiveHeadId? }` → header + lines (`poDetailsId, stockItemId, itemName, uom, mainUom, altUom, conversion, orderedQty, receivedQty, remainingQty, cost`) |
| `/Setting/SaveRMSPOReceive` | `receive_receive_asset` (new) / `receive_receive_asset` (edit) | Request + `poId`, `warehouseId` (required), `challanNo`, `post` (bool, replaces `PoReceiveIsDraft`; old flag still read), `updatedAt`, lines + `poDetailsId`; files as base64 in `fileUploads`. Response `receiveHeadId, receiveNumber, updatedAt`. Codes → messages, `status: false` on refusal |
| `/Setting/GetPOReceiveById` | `viewlist_receive_asset` | Any user of the company; + `receiveNumber`, `updatedAt`, `isDraft`, attachment links; "Receipt not found" |
| `/Setting/DeletePoReceive` (**new**) | `receive_receive_asset` | `{ receiveHeadId }` |
| `/Setting/GetPORecList`, `GetPORecDetailById`, `GetPurchaseOrderReceive` | — | Kept; `[CompanyAuthorize]` (D6, R11) |

Messages (`status: false`): `Purchase order not found` · `This purchase order can't be received (it's draft, cancelled or fully received).` · `Select a location` · `Received date can't be in the future` · `Received date can't be before the purchase order date` · `Enter a received quantity for at least one item` · `Damaged can't be more than received` · `Only {n} {uom} left on the purchase order for {item}` · `{item} is in a unit the raw material doesn't use ({uom})` · `Enter the invoice no or challan no` · `Invoice {no} is already recorded for this vendor` · `Posted receipts can't be changed.` · `Receipt not found` · `This receipt was changed by someone else. Reload and try again.` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.`. 401 for no / invalid / SuperAdmin token.

Success: "Receipt saved as draft", "Receipt posted", "Receipt deleted".
