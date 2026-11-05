# Report → Stock Check Report

The **Stock Check Report** is the history of physical counts: one row for every item counted, in a date range, at the user's locations. It shows when, where and by whom the item was counted, the counted quantity, its value and the note. The whole result can be exported to CSV.

Status (2026-11-04): **🟢 Built · SQL not yet run · testing deferred.** Plan: [`RMS_API/specs/stock-check-report/plan.md`](../../../RMS_API/specs/stock-check-report/plan.md). UI / UX plan: [`ui-ux.md`](./ui-ux.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `SCR`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-count-history.md`](./01-view-count-history.md) | See what was counted in a period at my locations; filter, export |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| R1 | **The report shows 0 and "—" for counts that exist.** Stock Check saves `StockCheckDate` with the date **and time**, but `fun_Get_Min_Max_Stock_*` compare it with exact equality against a midnight date, so they never match | [01](./01-view-count-history.md) |
| R2 | **Export CSV always fails**: the screen calls `/Setting/GetStockCheckExportReportData`, which doesn't exist in the API | [01](./01-view-count-history.md) |
| R3 | The list is built from `Stock` + `ItemWarehouseMap`, not from the counts: items never counted show 0 as if counted, and counts at a location the item is no longer mapped to don't show | [01](./01-view-count-history.md) |
| R4 | Only one day and only two counts of it: yesterday's last and the chosen day's first; a later count the same day is invisible | [01](./01-view-count-history.md) |
| R5 | No location rule on the server: "All" returns every location of the company to anyone, and any location id is accepted; `[Authorize]` with no permission check | [01](./01-view-count-history.md) |
| R6 | The time has no AM / PM and the date ignores the company format; if two counts share a time the functions fail with a "more than one value" error | [01](./01-view-count-history.md) |
| R7 | Help text promises "system and counted quantities and the variance", which the report never showed; 1000 rows loaded at once, no search, old page design | [01](./01-view-count-history.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Count history**: one row per count in a date range, newest first (not a one-day comparison) | 2026-11-04 |
| Q2 | **No variance**: the report shows the counted quantity only; no system quantity or variance is calculated or stored, and the help text stops promising it | 2026-11-04 |
| Q3 | **Counted value with a total**: value = counted quantity × the item's unit cost, with a total for the filtered result. Q2 and Q3 were answered together: with no variance, the value applies to the counted quantity (approved 2026-11-04) | 2026-11-04 |
| D1 | **Own locations only**, enforced on the server (same as Stock Report) | 2026-11-04 (default) |
| D2 | Filters: date range (default last 7 days, Apply to confirm), search (item name / alias), location, inventory type tabs with counts (as in Stock Report). Range up to 366 days | 2026-11-04 (default) |
| D3 | The count date is the calendar day of `StockCheckDate`, so rows with or without a time both match; the time is `StockCheckTime`, otherwise `CreatedAt`'s time | 2026-11-04 (default) |
| D4 | Quantities show in the main unit: a row saved in the alternate unit is divided by the conversion; a row in any other unit shows as saved, with value "—", and is left out of the total | 2026-11-04 (default) |
| D5 | Unit cost = `fn_GetItemUnitCost` (**today's** cost, as Stock Report), not the cost on the count date; the company's currency | 2026-11-04 (default) |
| D6 | The zero rows written when an item is created aren't counts and are hidden (`Qty = 0`, same `CreatedAt` as the item). A real count of 0 always shows | 2026-11-04 (default) |
| D7 | Permission `view_stock_check_report` for the report and export | 2026-11-04 (default) |
| D8 | Export: CSV of every filtered count (no paging, cap 50 000), same columns as the screen | 2026-11-04 (default) |
| D9 | `/Setting/GetStockCheckReportData` is kept for other callers, with `[CompanyAuthorize]` + permission; its dead export path is removed from the UI | 2026-11-04 (default) |

## Known limits
- A location added to an existing item writes a zero row with a different time, which can't be told apart from a real count of 0. It shows as a count of 0; the pre-check lists how many exist.
- Value uses today's cost, so an old count's value changes when the cost changes (D5).

## Out of scope
System quantity and variance (Q2); filter by the person who counted; approving or editing counts; stock movement over a period (Opening Stock Report); the same exact-date comparison in the Requisition consumption functions (`fun_GetConsumption`, `fun_GetConsumption_Main`), which is a separate fix.
