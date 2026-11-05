# API contract — Wastage Report

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetWastageHistoryReport` (**new**) | `view_wastage_report` | `{ fromDate, toDate, locationId, inventoryTypeId, reasonId, search, view: "Entries" \| "Item" \| "Reason", pageNumber, pageSize (1–100) }` → rows (`rowKey, recordedAt, warehouseName, stockItemId, itemName, itemAlias, inventoryTypeName, reason, qty, uom, unitCost, value, recordedBy, entryCount, itemCount`) + `totalCount`, `totalEntries`, `totalValue` |
| `/Setting/ExportWastageHistoryReport` (**new**) | `view_wastage_report` | Same filters and view, no paging (cap 50 000 rows) → same row shape |
| `/Setting/GetWastageReport` | `view_wastage_report` | Kept; `[CompanyAuthorize]` (D10) |

`value` is `null` for an entry in an unknown unit (D4). `fromDate` / `toDate` default to today when missing; an unknown `view` means `Entries`.

Messages: "Choose a period of up to one year." · "The end date can't be before the start date." · "You don't have permission to perform this action." · "Something went wrong. Please try again." 401 for no / invalid / SuperAdmin token.
