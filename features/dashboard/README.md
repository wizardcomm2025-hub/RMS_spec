# Dashboard

The **Dashboard** is the first screen after sign-in. It tells a restaurant manager what needs doing today and how the business is going: a **Needs attention** list with links, KPI cards (sales, stock value, low / out of stock, wastage, production, open purchase orders), a daily trend and two short lists (items to reorder, top wasted items). It reads existing data only; nothing is saved from it.

Status (2026-11-04): **🟢 Built · SQL not yet run · testing deferred.** Plan: [`RMS_API/specs/dashboard/plan.md`](../../../RMS_API/specs/dashboard/plan.md). UI / UX plan: [`ui-ux.md`](./ui-ux.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `DSH`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-business-figures.md`](./01-view-business-figures.md) | See KPI cards, the daily trend and the two lists for my locations and a date range |
| 02 | [`02-needs-attention.md`](./02-needs-attention.md) | See what is waiting for me today and jump to it |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| H1 | **Counts are capped by the queries**: Requisitions and Pending Receives `TOP 5`, Transfers `TOP 5`, My Requisitions `TOP 6`, so a card can never go above that number | [01](./01-view-business-figures.md), [02](./02-needs-attention.md) |
| H2 | **"Stock items tracked", "Below Reorder" and "Stock Health" are computed from `TOP 10`** of the worst items, so a company with 500 healthy items shows about 0% healthy | [01](./01-view-business-figures.md) |
| H3 | Stock figures use only the user's **default location**, without saying so | [01](./01-view-business-figures.md) |
| H4 | **"Pending Receives" isn't what the user must receive**: it lists transfers the user *created*, with no status filter | [02](./02-needs-attention.md) |
| H5 | **Today's stock checks are missed**: `StockCheckDate BETWEEN from AND to` with `to` at midnight, while new counts are stored with the time | [02](./02-needs-attention.md) |
| H6 | The **store filter on Recent Requisitions does nothing** (the condition is commented out) and the list shows every location of the company | [02](./02-needs-attention.md) |
| H7 | Transfers are joined to `UserWarehouseMapping` without a user, so one transfer can appear once per mapped user | [02](./02-needs-attention.md) |
| H8 | Quantities are compared and charted **without converting units**; "Stock Out Products" actually lists anything below the reorder level | [01](./01-view-business-figures.md) |
| H9 | **Misleading indicators**: up / down arrows and percentages with no real meaning (e.g. pending ÷ (pending + transfers)); the date selector sits on one chart but changes the whole page, while stock figures ignore it | [01](./01-view-business-figures.md) |
| H10 | **No money figures** at all: no stock value, wastage, production cost, purchases or sales | [01](./01-view-business-figures.md) |
| H11 | One spinner blocks the page until all calls finish; a failed call is only logged, so the card shows a wrong 0 | [01](./01-view-business-figures.md) |
| H12 | No server permission check (`[Authorize]` only, which also lets a super-admin token in with no user); page title is the template's "Vendor Dashboard - Bazaar…" | [01](./01-view-business-figures.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Rebuild for operations**: Needs attention + KPI cards + trend + two lists; the old widgets are removed | 2026-11-04 |
| Q2 | **Figures shown**: stock, wastage and production, purchases, sales (POS) | 2026-11-04 |
| Q3 | **One filter bar**: location (default all my locations, enforced on the server) and date range (default this month). "Right now" figures ignore the dates, and each card says which kind it is | 2026-11-04 |
| Q4 | **Sections follow existing permissions**, checked on the server (table in D3) | 2026-11-04 |
| D1 | Same definitions as the module screens, so numbers agree: stock value, Low and Out as in the Stock Report; wastage value as in the Wastage Report; production cost = `FinalProductionCost`; PO statuses and receipt state as in Purchase Order; open dispatch as in Inbound Requisition; to receive = transfers in transit to my locations | 2026-11-04 (default) |
| D2 | **Sales** = bills (`BillHead`) by `BillDate` at the location (`OutletId`): total including tax, bill count, average bill, unpaid amount. `BillHead` has no cancel state, so every bill counts | 2026-11-04 (default) |
| D3 | Permissions: page `view_dashboard` · stock `view_stock_report` · wastage `view_wastage_report` · production `viewlist_product` · purchases `viewlist_purchase_order` · sales `view_dashboard_sales` (**new**, granted to each company's Administrator role by a data script, because no sales permission exists and these are sensitive figures) · attention rows: table in [02](./02-needs-attention.md) | 2026-11-04 (default) |
| D4 | Purchases are **company-wide**: a PO has no location. The location filter doesn't apply, and the card says "All locations" | 2026-11-04 (default) |
| D5 | No comparison with the previous period and no trend arrows in this version: a figure is shown only if it is true | 2026-11-04 (default) |
| D6 | Each section loads on its own, with its own skeleton and error + Retry. A failure never shows 0 | 2026-11-04 (default) |
| D7 | Old dashboard endpoints stay for other callers, moved to `[CompanyAuthorize]` + `view_dashboard`; their procedures are unchanged. The UI stops using them | 2026-11-04 (default) |
| D8 | Calendar days in server local time, as the module reports. Range limited to 366 days | 2026-11-04 (default) |

## Out of scope
Customisable widgets (`DashboardWidgetCommon` / `DashboardWidgetUserWise` stay unused); previous-period comparison; food-cost percentage; sales by item, channel or payment mode; a POS review (bills are read as stored); auto-refresh; export.
