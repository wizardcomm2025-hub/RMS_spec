# API contract — Stock Report

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetStockReport` (**new**) | `view_stock_report` | `{ pageNumber, pageSize (1–100), locationId, inventoryTypeId, show: "All" \| "Low" \| "Out", search }` → rows (`stockItemId, itemName, itemAlias, inventoryTypeName, uom, reorderLevel, warehouseId, warehouseName, qty, unitCost, value, status`) + `totalCount`, `totalValue` |
| `/Setting/ExportStockReport` (**new**) | `view_stock_report` | Same filters, no paging (cap 50 000 rows) → same row shape |
| `/Setting/GetTotQtyStockItemWiseRpt_Page`, `GetStoQtyWarehouseWiseRpt`, `StockReprtForExport` | `view_stock_report` | Kept; `[CompanyAuthorize]` (D5) |

`status`: `Out` (qty ≤ 0), `Low` (0 < qty ≤ reorder level, reorder level > 0), `''` otherwise. Messages: "You don't have permission to perform this action." · "Something went wrong. Please try again." 401 for no / invalid / SuperAdmin token.
