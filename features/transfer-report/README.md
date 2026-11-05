# Report → Transfer Report

The **Transfer Report** shows stock moved between locations in a date range: one row per transfer (number, date, from → to, who sent it, who received it, status, value sent, value short), expanding to its item lines (sent, received, short, shortage reason). It covers transfers where **either end** is one of the user's locations. The result can be exported to CSV, one line per item.

Status (2026-11-04): **🟢 Built · SQL not yet run · testing deferred.** Plan: [`RMS_API/specs/transfer-report/plan.md`](../../../RMS_API/specs/transfer-report/plan.md). UI / UX plan: [`ui-ux.md`](./ui-ux.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `TRP`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-transfers.md`](./01-view-transfers.md) | See transfers in a period at my locations and what arrived; expand, filter, export |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| T1 | **"Transferred By" is wrong**: it shows the person who created the sending location (`Warehouse.CreatedBy`), not who sent the transfer | [01](./01-view-transfers.md) |
| T2 | **"Received By" is invented**: it shows the first user mapped to the destination, even for transfers nobody has received, not `Transfer.ReceiveBy` | [01](./01-view-transfers.md) |
| T3 | **Goods in transit look lost**: Difference = sent − 0 for transfers not yet received, and no status is shown, so a transfer on the road looks like a full shortage | [01](./01-view-transfers.md) |
| T4 | The filter uses `CreatedAt` while the screen shows `TransferDate`, so rows can appear to be outside the chosen dates; a row at midnight the day after the end date is included | [01](./01-view-transfers.md) |
| T5 | No location rule and no permission check on the server (`[Authorize]`): anyone sees every transfer of the company | [01](./01-view-transfers.md) |
| T6 | Inactive transfers and lines are counted; a transfer with no lines shows as a blank row | [01](./01-view-transfers.md) |
| T7 | No transfer or requisition number, so a row can't be traced; no status, no value; search needs a button and covers item names only | [01](./01-view-transfers.md) |
| T8 | No paging (everything loads at once), date format ignores the company setting, export date is fixed to `dd.MM.yyyy`, old page design | [01](./01-view-transfers.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **One row per transfer, expanding to item lines**; the export has one line per item | 2026-11-04 |
| Q2 | **Either end is mine**: transfers sent from or received at one of the user's locations; enforced on the server | 2026-11-04 |
| Q3 | **Sent value and short value**, with totals for the filtered result, at the item's unit cost, in the company's currency | 2026-11-04 |
| D1 | Status tabs with counts: All · In transit · Received · With shortage (Recipe tab pattern). *With shortage* = received with at least one line short | 2026-11-04 (default) |
| D2 | Filters: date range on `TransferDate` (default last 7 days, Apply to confirm, up to 366 days) · search (transfer number, requisition number, item name / alias) · Location (my locations; matches either end) | 2026-11-04 (default) |
| D3 | Searching an item shows the whole transfer, with all its lines | 2026-11-04 (default) |
| D4 | **Short** exists only once a transfer is received: Short = sent − received. In transit shows Received and Short as "—", and isn't in the short total | 2026-11-04 (default) |
| D5 | A legacy line where received > sent shows as "2 kg over"; it isn't a shortage and adds nothing to the short value | 2026-11-04 (default) |
| D6 | Quantities show in the line's unit. For value, an alternate-unit line is divided by the conversion; a line in any other unit shows value "—" and is left out of the totals | 2026-11-04 (default) |
| D7 | Unit cost = `fn_GetItemUnitCost` (**today's** cost, as Stock Report), not the cost on the transfer date | 2026-11-04 (default) |
| D8 | Sent by = `Transfer.CreatedBy`. Received by = `Transfer.ReceiveBy`, or the first line's `ReceiveBy` on old rows; shown only once received | 2026-11-04 (default) |
| D9 | Permission `view_transfer_report` for the report and export | 2026-11-04 (default) |
| D10 | Export: CSV of every line of the filtered transfers (no paging, cap 50 000) | 2026-11-04 (default) |
| D11 | `/Setting/GetTransferReprt` and `GetTransferReprtForExport` are kept for other callers, with `[CompanyAuthorize]` + permission | 2026-11-04 (default) |

## Known limits
- Value uses today's cost, so an old transfer's value changes when the cost changes (D7).
- A transfer shows to anyone mapped to either end, so a user sees the other location's name and the transfer's values even if not mapped there (Q2).

## Out of scope
Vehicle and driver details (Item Receive shows them); editing or reversing a transfer; returning a shortage; dashboard transfer cards; item-wise totals across transfers.
