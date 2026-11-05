# API contract — Opening Stock Report (stock movement)

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`, permission `view_opening_stock_report`.

| Endpoint | Change |
|---|---|
| `/Setting/GetOpeningStockReport` (**reshaped**) | `{ fromDate, toDate (yyyy-MM-dd), pageNumber, pageSize (1–100), locationId, inventoryTypeId, show: "All" \| "Moved" \| "Negative", search }` → rows (`stockItemId, itemName, inventoryTypeName, uom, warehouseId, warehouseName, openingQty, inQty, outQty, countQty, closingQty, unitCost, closingValue`) + `totalCount, openingValue, closingValue, ledgerStart` |
| `/Setting/ExportOpeningStockReport` (**reshaped**) | Same filters, no paging (cap 50 000) |
| `/Setting/GetStockMovements` (**new**) | `{ stockItemId, locationId (null = all mine), fromDate, toDate }` → rows (`moveDate, recordedAt, warehouseName, moveType, refNumber, qty, balanceAfter, userName`), first row type `Opening` |
| `/Setting/GetItemWiseOpStockDif`, `GetWarehouseWiseOpStockDif`, `GetWarehouseWiseOpStockExportReport` | Kept guarded (v1) |

Messages: "The dates can't be in the future" · "From date must be on or before To date" · "You don't have permission to perform this action." · "Something went wrong. Please try again." 401 for no / invalid / SuperAdmin token.
