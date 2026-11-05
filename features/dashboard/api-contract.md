# API contract — Dashboard

`POST /Setting/...`, `[CompanyAuthorize]`, envelope `{ Status, Message, Data }`. Company and user come from the token. Every endpoint needs `view_dashboard` plus the permission shown; otherwise `Status false`, "You don't have permission to perform this action."

| Endpoint | Extra permission | Request | Data |
|---|---|---|---|
| `GetDashboardSections` | — | `{}` | `{ attention, stock, wastage, production, purchases, sales }` (booleans: what this user may see) |
| `GetDashboardAttention` | row-level (see [02](./02-needs-attention.md)) | `{ locationId }` | `{ toApprove, toDispatch, toReceive, poAwaitingDelivery, poDraft, locationsNotCounted }`; a row the user may not see is `null` |
| `GetDashboardStock` | `view_stock_report` | `{ locationId }` | `{ stockValue, itemCount, lowCount, outCount, items: [{ stockItemId, itemName, locationName, qty, uom, reorderLevel, status }] }` |
| `GetDashboardPeriod` | `view_wastage_report` and / or `viewlist_product` | `{ fromDate, toDate, locationId }` | `{ wastageValue, wastageEntries, productionCost, productionBatches, days: [{ day, wastageValue, productionCost }], topWasted: [{ stockItemId, itemName, qty, uom, value }] }`; parts the user may not see are `null` |
| `GetDashboardPurchases` | `viewlist_purchase_order` | `{}` | `{ openPoCount, openPoValue }` |
| `GetDashboardSales` | `view_dashboard_sales` | `{ fromDate, toDate, locationId }` | `{ salesTotal, billCount, unpaidAmount, days: [{ day, salesTotal }] }` |

Validation messages: "Choose both dates." · "From date can't be after To date." · "Choose a range of up to 366 days."

`locationId` null / 0 = all my locations. Dates `yyyy-MM-dd`. Unexpected error: "Something went wrong. Please try again." (SQL text is logged, not returned).

Existing endpoints kept for other callers, now `[CompanyAuthorize]` + `view_dashboard`: `GetRequisitionStatusForuser`, `RequistionViewDashBoard`, `GetStockCheckStatus`, `ShowCurrentStockListWidget`, `GetTransferWidget`, `GetMyRequisitionWidget`, `GetPendingReceivesWidget`, `CurrentStockOnDashboard`.
