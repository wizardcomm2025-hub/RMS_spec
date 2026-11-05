# API contract — Barcode Report

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetBarcodeStockReport` (**new**) | `view_barcode_report` | `{ locationId, inventoryTypeId, show: "InStock" \| "Expiring" \| "Expired" \| "UsedUp", search, pageNumber, pageSize (1–100) }` → one row per barcode of the page's items: `stockItemId, itemName, itemAlias, inventoryTypeName, mainUom, itemQty, itemValue, barcodeCount, nearestExpiry, barcodeId, barcodeNumber, warehouseName, vendorName, mfgDate, expDate, status, initialQty, remainingQty, uom, unitCost, value` + `totalCount` (items), `totalBarcodes`, `totalValue` |
| `/Setting/ExportBarcodeStockReport` (**new**) | `view_barcode_report` | Same filters, no paging (cap 50 000 rows) → same row shape |
| `/Setting/TotalQtyOfBarcodeReport`, `GetBarcodeReportByItemId`, `GetBarcodeExportReport` | `view_barcode_report` | Kept; `[CompanyAuthorize]` (D10) |

`status` is `"UsedUp"`, `"Expired"`, `"Expiring"` or `""`. `value` is `null` for a barcode in an unknown unit. An unknown `show` means `InStock`. Dates are ISO dates or `null`.

Messages: "You don't have permission to perform this action." · "Something went wrong. Please try again." 401 for no / invalid / SuperAdmin token.
