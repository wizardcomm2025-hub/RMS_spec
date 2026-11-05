# API contract — Transfer Report

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetStockTransferReport` (**new**) | `view_transfer_report` | `{ fromDate, toDate, locationId, show: "All" \| "InTransit" \| "Received" \| "Short", search, pageNumber, pageSize (1–100) }` → one row per line of the page's transfers: `transferId, transferNumber, requisitionNumber, transferDate, fromWarehouseName, toWarehouseName, sentBy, receivedBy, receivedAt, statusId, statusName, transferDetailId, stockItemId, itemName, itemAlias, uom, sentQty, receivedQty, shortQty, unitCost, sentValue, shortValue, receiveNote` + `totalCount` (transfers), `totalSentValue`, `totalShortValue` |
| `/Setting/ExportStockTransferReport` (**new**) | `view_transfer_report` | Same filters, no paging (cap 50 000 rows) → same row shape |
| `/Setting/GetTransferReprt`, `GetTransferReprtForExport` | `view_transfer_report` | Kept; `[CompanyAuthorize]` (D11) |

`receivedQty`, `shortQty`, `shortValue`, `receivedBy`, `receivedAt` are `null` until the transfer is received. `sentValue` and `shortValue` are `null` for a line in an unknown unit. `fromDate` / `toDate` default to today when missing; an unknown `show` means `All`.

Messages: "Choose a period of up to one year." · "The end date can't be before the start date." · "You don't have permission to perform this action." · "Something went wrong. Please try again." 401 for no / invalid / SuperAdmin token.
