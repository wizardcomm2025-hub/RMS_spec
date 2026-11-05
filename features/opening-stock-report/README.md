# Report → Opening Stock Report (stock movement)

The **Opening Stock Report** shows, for a date range and each item at the user's locations, the stock the item had **at the start of the range (opening)**, what came **in** and went **out** during it, **adjustments**, and the stock **at the end (closing)** — opening + in − out ± adjustments = closing — with the closing value.

It is built on a new **stock ledger** (`dbo.StockLedger`): every change to `dbo.Stock`, from any screen, is recorded as a movement in the item's main unit, with its type (purchase, transfer in / out, production, wastage, count correction, sale, other), document and date. The ledger starts on the day it is switched on (seeded with that day's stock); the report can't look before that day.

Status (2026-11-04): **Built (v2, stock ledger) — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/opening-stock-report/plan.md`](../../../RMS_API/specs/opening-stock-report/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `OSR`

## History of this spec
- 2026-11-04 v1 (built, superseded): compared the previous day's last Stock Check count with the day's first count. The user found the meaning wrong: opening stock is the stock at the start of the day, not a count. Replaced by this version; `Sp_GetOpeningStockReport` is rewritten, the page rebuilt again.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-stock-movement.md`](./01-view-stock-movement.md) | Opening, in, out, adjustments and closing per item for a date range; per location; export |
| 02 | [`02-view-movements.md`](./02-view-movements.md) | See the individual movements behind an item's figures |
| 03 | [`03-stock-ledger.md`](./03-stock-ledger.md) | Every stock change is recorded (system behaviour) |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| O1 | There is **no record of stock movements**: every module overwrites `Stock.Qty`, so stock on a past date can't be known; the old report compared Stock Check counts and called it "opening" | [03](./03-stock-ledger.md), [01](./01-view-stock-movement.md) |
| O2 | The old procedures returned a hard-coded difference of 0 and matched counts on the date at midnight (counts with a time were missed) | [01](./01-view-stock-movement.md) |
| O3 | POS sales and Stock Check corrections change stock without any document, so nobody can see why stock went down | [03](./03-stock-ledger.md) |
| O4 | Old report: only mapped items with a stock row, units mixed, no location / company check, 1000 rows in the browser, no server permission check | [01](./01-view-stock-movement.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Stock ledger**: every stock change writes a movement row; opening = balance before the range | 2026-11-04 |
| Q2 | **Date range**: opening at the start of *from*, movements in [from, to], closing at the end of *to*; one day = from = to (default today) | 2026-11-04 |
| D1 | Captured by a **trigger on `dbo.Stock`** (insert / update / delete): change = new − old quantity of active rows, converted to the main unit (alternate-unit rows ÷ conversion). Nothing that changes stock can skip it | 2026-11-04 (default) |
| D2 | **Type, document and business date** come from `SESSION_CONTEXT` set by the saving procedure (`StockMoveType`, `StockMoveRef`, `StockMoveDate`); untagged changes are type *Other* dated today. Tagged: PO Receive (*Purchase*), Dispatch (*TransferOut*), Item Receive (*TransferIn*), Production (*Production*: + produced / − used), Wastage save / cancel (*Wastage*), Stock Check (*Count*), POS (*Sale*) | 2026-11-04 (default) |
| D3 | **Seed**: one *Opening* row per item + location with today's stock, dated the day the ledger is switched on; dates before it show "Stock history starts on {date}" and are moved to that date | 2026-11-04 (default) |
| D4 | Report columns: Opening · In (purchase + transfer in + produced + other in) · Out (transfer out + used in production + sold + wasted + other out) · Count adj. · Closing · Closing value ₹ (closing × `fn_GetItemUnitCost`); positive / negative of a type go to In / Out | 2026-11-04 (default) |
| D5 | Items shown: any opening, movement or closing ≠ 0 in the range at the location; filters: type tabs with counts (as Stock Report), date range, search, location (my locations), Show: All / With movement / Negative closing | 2026-11-04 (default) |
| D6 | Movements list (02): date, type, document, quantity (+/−), balance after, by — per item and location | 2026-11-04 (default) |
| D7 | Permission `view_opening_stock_report`; own locations only; export CSV | 2026-11-04 (default) |

## Out of scope
Rebuilding history before the ledger start; tagging the old screens that still change stock (they record as *Other*); costing movements at their historic cost (value uses today's unit cost).
