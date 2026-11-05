# Report → Stock Report

The **Stock Report** shows how much of each item is in stock right now at the user's locations, in the item's **main unit**, with its **value** (quantity × the item cost Recipe and Production use) and a **low-stock** flag against the item's reorder level. One row per item; expanding it shows the quantity per location. The whole result can be exported to CSV.

Status (2026-11-04): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/stock-report/plan.md`](../../../RMS_API/specs/stock-report/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `SRP`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-stock.md`](./01-view-stock.md) | See current stock and value per item at my locations; filter, expand per location, export |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| S1 | Stock is counted **only where the item is mapped to the location** (`ItemWarehouseMap`): stock added by PO Receive, Item Receive or Production at an unmapped location never shows — it looks as if the stock didn't go up | [01](./01-view-stock.md) |
| S2 | Quantities are **summed across units**: a row kept in grams is added to a row in kg (500 g + 2 kg = "502"); the list shows no unit at all | [01](./01-view-stock.md) |
| S3 | The item list counts inactive stock rows, the per-location breakdown doesn't, so the two disagree; deleted locations are counted | [01](./01-view-stock.md) |
| S4 | No location rule on the server: "All" returns every location of the company to anyone; the per-location breakdown takes any item id with **no company check** | [01](./01-view-stock.md) |
| S5 | Money columns are the standard purchase cost and sales price (often 0 or stale), not what the stock is worth | [01](./01-view-stock.md) |
| S6 | Loads 1000 rows at once in the browser, no search, no low-stock view, "Get" button, no server permission checks, not on the standard design | [01](./01-view-stock.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Own locations only**: everyone sees the locations they're mapped to (User List); enforced on the server | 2026-11-04 |
| Q2 | **Stock or mapped**: an item appears for a location when it has an active stock row there or is mapped to it (mapped without stock shows 0); stock is never hidden for lack of a mapping | 2026-11-04 |
| Q3 | **Stock value**: unit cost = `fn_GetItemUnitCost` (last purchase cost / recipe cost, per main unit); value = quantity × unit cost; total value of the filtered result shown | 2026-11-04 |
| Q4 | **Low-stock highlight + filter**: *Low* when 0 < quantity ≤ reorder level (reorder level > 0), *Out* when quantity ≤ 0; Show: All / Low stock / Out of stock | 2026-11-04 |
| D1 | Quantities in the item's main unit; stock rows kept in the alternate unit converted (÷ conversion); rows in any other unit ignored and counted by the pre-check | 2026-11-04 (default) |
| D2 | Filters: search (name / alias), location (my locations, default all), inventory type, Show; paged by item on the server, sorted by name | 2026-11-04 (default) |
| D3 | Export: CSV of every filtered item × location (no paging), same columns as the screen | 2026-11-04 (default) |
| D4 | Permission `view_stock_report` for the report and export | 2026-11-04 (default) |
| D5 | Old endpoints (`GetTotQtyStockItemWiseRpt_Page`, `GetStoQtyWarehouseWiseRpt`, `StockReprtForExport`) kept for other callers, `[CompanyAuthorize]` + permission | 2026-11-04 (default) |

## Out of scope
Stock movement over a period (opening / in / out / closing) — that is the Opening Stock Report; merging duplicate stock rows (the pre-check lists them); stock as on a past date.
