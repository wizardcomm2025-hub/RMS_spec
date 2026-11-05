# API contract — Stock Check Report

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetStockCheckReport` (**new**) | `view_stock_check_report` | `{ fromDate, toDate, locationId, inventoryTypeId, search, pageNumber, pageSize (1–100) }` → rows (`stockCheckId, countedAt, warehouseId, warehouseName, stockItemId, itemName, itemAlias, inventoryTypeName, qty, uom, unitCost, value, countedBy, note`) + `totalCount`, `totalValue` |
| `/Setting/ExportStockCheckReport` (**new**) | `view_stock_check_report` | Same filters, no paging (cap 50 000 rows) → same row shape |
| `/Setting/GetStockCheckReportData` | `view_stock_check_report` | Kept; `[CompanyAuthorize]` (D9) |

`value` is `null` for a row in an unknown unit (D4). `fromDate` / `toDate` default to today when missing.

Messages: "Choose a period of up to one year." · "The end date can't be before the start date." · "You don't have permission to perform this action." · "Something went wrong. Please try again." 401 for no / invalid / SuperAdmin token.
