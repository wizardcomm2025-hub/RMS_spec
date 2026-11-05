# Stock → Wastage

**Wastage** records stock that was spoiled, expired, spilled or otherwise lost at a location, with a reason. Saving a wastage line takes the quantity out of the location's stock (`dbo.Stock`) and adds a `dbo.Wastage` row (reported in the Wastage Report and dashboard widgets). A line can be linked to a scanned **barcode**, whose remaining quantity goes down too.

Status (2026-11-03): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/wastage/plan.md`](../../../RMS_API/specs/wastage/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `WST`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-wastage-sheet.md`](./01-wastage-sheet.md) | Open the wastage sheet for a location; find items by search or barcode |
| 02 | [`02-record-wastage.md`](./02-record-wastage.md) | Record wastage lines (all entered, or one row) and reduce stock |
| 03 | [`03-cancel-today.md`](./03-cancel-today.md) | See today's wastage at the location and cancel an entry the same day |

## Defects this feature fixes (found 2026-11-03)

| # | Defect | Fixed by |
|---|---|---|
| W1 | Stock is reduced **`WHERE StockId = @id` from the request** — any company's stock row can be changed; an item without that id gets a wastage row but **no stock change** | [02](./02-record-wastage.md) |
| W2 | **No unit conversion**: 500 g of an item kept in kg takes 500 kg off stock | [02](./02-record-wastage.md) |
| W3 | Location, item and reason aren't checked (company, user's location, active); **reason 0 is saved** although the help text says every line needs one | [02](./02-record-wastage.md) |
| W4 | **Stock can go negative** — no check against what is on hand | [02](./02-record-wastage.md) |
| W5 | Barcode remaining quantity is reduced by an id from the request, with no company check and no unit check | [02](./02-record-wastage.md) |
| W6 | No transaction (cursor; a failure half-way leaves half the lines saved) | [02](./02-record-wastage.md) |
| W7 | After saving, the typed quantities stay on screen — **pressing Save again records the wastage twice**; no confirmation of what was saved | [02](./02-record-wastage.md) |
| W8 | No server permission checks; the barcode search ignores the location | all |
| W9 | Mistakes can't be undone; collapsed accordions hide items; not on the standard page design | [01](./01-wastage-sheet.md), [03](./03-cancel-today.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Block wastage above stock on hand** (converted to the main unit), checked on the server under a lock | 2026-11-03 |
| Q2 | **Flat sheet like Stock Check**: one table with a Group column, search, blank qty + unit + reason per row, row save, "Save wastage (n)" footer | 2026-11-03 |
| Q3 | **Cancel the same day**: a "Recorded today" list; cancelling puts the quantity back into stock (and the barcode) and hides the entry from reports | 2026-11-03 |
| Q4 | **Barcode scan kept**: scanning finds the item, filters the sheet to it and links the line to that barcode | 2026-11-03 |
| D1 | Reason required on every line (active reasons of the company); quick-add reason stays | 2026-11-03 (default) |
| D2 | Wastage rows stored in the **main unit** from now on (report sums one unit) | 2026-11-03 (default) |
| D3 | Only the user's locations; only items with stock > 0 at the location are listed | 2026-11-03 (default) |
| D4 | Cancelled entries are `IsActive = 0`; the Wastage Report and the two dashboard wastage widgets count active rows only | 2026-11-03 (default) |
| D5 | Old endpoints (`AddEditWastage`, `GetStockWastage`, `SearchWastageByBarCode`) kept for other callers; company-guarded, permission-checked; the save goes through the new safe save | 2026-11-03 (default) |

## Out of scope
Editing an entry (cancel and record again); cancelling after the day ends; correcting stock already wrong from W1/W2/W4 (the pre-check lists suspects); the Wastage Report screen.

## Found, not fixed
| Item | Note |
|---|---|
| Old procedures | `SP_AddEditWastage`, `SP_Get_Stock_Wastage`, `SP_SearchWastageItemByBarCode` stay in the database with their bugs; the API no longer calls them for saving |
| Old list endpoints | `GetStockWastage` and `SearchWastageByBarCode` (kept for other callers) still use the old procedures, which don't check the user's location |
| Duplicate stock rows | Where an item has two stock rows at one location, wastage is taken from the larger one (Stock Check pre-check lists duplicates) |
