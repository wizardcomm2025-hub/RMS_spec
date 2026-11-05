# API contract — Purchase Order

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetPurchaseOrderList` (**new**) | — | `{ pageNumber, pageSize, fromDate, toDate, status, vendorId, search }` → rows + `totalCount` |
| `/Setting/GetGetPurchaseOrderDetailsbyId` | — | + `updatedAt`, `hasReceipt`; "Purchase order not found" |
| `/Setting/SavePODetails` | `create_purchase_order` (new) / `edit_purchase_order` (edit) | Codes → messages; `status: false` on refusal; `status` only "Saved" / "Submit"; `PoNumber` ignored; request + `updatedAt`; response + `poNumber`, `updatedAt` |
| `/Setting/DeletePoDetailsById` | `edit_purchase_order` | Draft + company only |
| `/Setting/CancelPurchaseOrder` (**new**) | `edit_purchase_order` | `{ poId, reason }` |
| `/Setting/DeletePurchaseOrder` (**new**) | `edit_purchase_order` | `{ poId }` |
| `/Setting/GetGetPurchaseOrderDetails`, `GetGetPurchaseOrderDetailsby`, `GetRequisitionLineItemList`, `StockItemSuggestionRawItem`, `CheckOCRStockItem` | — | `[CompanyAuthorize]` |

Messages (`status: false`): `Only draft purchase orders can be changed.` · `Vendor not available` · `One or more items are not available` · `One or more requisition lines are already on another purchase order` · `Add at least one item` · `This purchase order has goods received against it and can't be cancelled.` · `Enter a reason for cancelling` · `Purchase order not found` · `This purchase order was changed by someone else. Reload and try again.` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.`. 401 for no / invalid / SuperAdmin token.

Success: "Purchase order saved as draft", "Purchase order submitted", "Purchase order cancelled", "Purchase order deleted".
