# Report → Wastage Report

The **Wastage Report** shows what was wasted in a date range at the user's locations, why, and what it cost. Three views: **Entries** (one row per wastage entry), **By item** and **By reason** (totals, biggest loss first), so both single events and recurring losses are visible. The current view can be exported to CSV.

Status (2026-11-04): **🟢 Built · SQL not yet run · testing deferred.** Plan: [`RMS_API/specs/wastage-report/plan.md`](../../../RMS_API/specs/wastage-report/plan.md). UI / UX plan: [`ui-ux.md`](./ui-ux.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `WRP`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-wastage.md`](./01-view-wastage.md) | See wastage in a period by entry, item or reason; filter, export |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| W1 | **The reason isn't shown**, although the help text promises it and every entry has one; no way to see why stock is being lost | [01](./01-view-wastage.md) |
| W2 | **Export CSV always fails**: the screen calls `/Setting/GetWastageExportReport`, which doesn't exist in the API | [01](./01-view-wastage.md) |
| W3 | **Choosing a location is likely to fail**: that path of `SP_Get_Wastage_Rpt` returns the date as `dd.MM.yyyy` text, but the API reads it as a date | [01](./01-view-wastage.md) |
| W4 | **Wrong rows**: the result model uses `StockItemId` as its key and is read with tracking, so a second entry for the same item comes back repeating the first entry's values | [01](./01-view-wastage.md) |
| W5 | No location rule and no permission check on the server (`[Authorize]`): "All" returns every location of the company, and any location id is accepted | [01](./01-view-wastage.md) |
| W6 | A row kept in the alternate unit isn't converted; no value, no totals, nothing shows which items or reasons cost the most | [01](./01-view-wastage.md) |
| W7 | A row at midnight the day after the end date is included; the date ignores the company format and hides the time | [01](./01-view-wastage.md) |
| W8 | Search filters only what's already loaded, on item name only; everything loads at once with no paging; needs a button; old page design | [01](./01-view-wastage.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Three views**: Entries · By item · By reason, with a toggle; the choice is remembered in the browser | 2026-11-04 |
| Q2 | **Wastage value with a total**: value = quantity × the item's unit cost, in the company's currency; total for the filtered result | 2026-11-04 |
| D1 | **Own locations only**, enforced on the server (same as the other reports) | 2026-11-04 (default) |
| D2 | Filters: date range on `CreatedAt` by calendar day (default last 7 days, Apply to confirm, up to 366 days) · search (item name / alias) · Location · Reason · inventory type tabs with counts (as in Stock Report). All filters apply to all three views | 2026-11-04 (default) |
| D3 | Entries: newest first. By item and By reason: highest value first, then name | 2026-11-04 (default) |
| D4 | Quantities show in the main unit: a row in the alternate unit is divided by the conversion. A row in any other unit shows as saved with value "—", is left out of the value total, and isn't added into By item's quantity | 2026-11-04 (default) |
| D5 | By reason shows no quantity, since items have different units: Reason · Entries · Items · Value · Share. By item: Item · Type · Quantity · Entries · Value · Share. Share = the row's value ÷ the total value | 2026-11-04 (default) |
| D6 | Unit cost = `fn_GetItemUnitCost` (**today's** cost, as Stock Report), not the cost on the wastage date | 2026-11-04 (default) |
| D7 | Cancelled entries (`IsActive = 0`) never count (Wastage D4). Legacy entries with no reason show as "No reason" | 2026-11-04 (default) |
| D8 | Permission `view_wastage_report` for the report and export | 2026-11-04 (default) |
| D9 | Export: CSV of the current view for every filtered row (no paging, cap 50 000) | 2026-11-04 (default) |
| D10 | `/Setting/GetWastageReport` is kept for other callers, with `[CompanyAuthorize]` + permission; `SP_Get_Wastage_Rpt` is unchanged | 2026-11-04 (default) |

## Known limits
Value uses today's cost, so an old entry's value changes when the cost changes (D6). Items with no cost show ₹0.00, as in the Stock Report.

## Out of scope
Trends over time and charts; comparing periods; cancelling or editing entries here (Wastage screen); filter by who recorded it; the dashboard wastage widgets.
