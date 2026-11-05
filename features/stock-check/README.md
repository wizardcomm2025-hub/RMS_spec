# Stock → Stock Check

A **stock check** is a physical count of what is on the shelf at one location. The user enters the counted quantity for the items they counted; the count is saved as a `dbo.StockCheck` row (history, used by the Stock Check Report and consumption) and the location's stock (`dbo.Stock`) is **set** to the counted quantity. The difference from the system quantity is the **variance**.

Status (2026-11-03): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/stock-check/plan.md`](../../../RMS_API/specs/stock-check/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `SCK`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-count-sheet.md`](./01-count-sheet.md) | Open the count sheet for a location: items with system stock, last count and variance |
| 02 | [`02-save-counts.md`](./02-save-counts.md) | Save the counted items (all entered rows, or one row) and set stock |

## Defects this feature fixes (found 2026-11-03)

| # | Defect | Fixed by |
|---|---|---|
| S1 | **"Save Stock" saves every item**: rows you didn't count go out as `00.00` and **set their stock to 0** | [02](./02-save-counts.md) |
| S2 | The bulk save reads company and user from the request body, which the screen never sends → count rows and new stock rows with **no company and no user** (invisible to the report) | [02](./02-save-counts.md) |
| S3 | The bulk save updates stock **`WHERE StockId = @id` from the request** — any company's stock row can be overwritten; the location isn't checked against the company or the user | [02](./02-save-counts.md) |
| S4 | **No unit conversion**: counting in the alternate unit (g) writes grams into the stock row and switches its unit | [02](./02-save-counts.md) |
| S5 | Bulk save: the note is never stored (cursor reads a variable instead of the column); no transaction | [02](./02-save-counts.md) |
| S6 | The single-row save refuses 0 — an item that has run out can't be recorded | [02](./02-save-counts.md) |
| S7 | No server permission checks; the list is merged in C# from three procedures with nested loops | all |
| S8 | No variance shown (the help text promises one); "Yesterday" column is really the last count; full-page spinner; not on the standard page design | [01](./01-count-sheet.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Count sheet, save entered only.** Blank = not counted (nothing saved), `0` = really empty. **Save counts** saves every entered row in one go; the row save button stays for single counts. No draft / post step | 2026-11-03 |
| Q2 | Count in the **main or alternate unit**; the server converts to the main unit (alt ÷ `Conversion`) before saving the count and setting stock | 2026-11-03 |
| Q3 | **Variance shown**, note optional: variance = counted (main unit) − system stock; quantity and %; stock is set to the counted quantity | 2026-11-03 |
| Q4 | **Counts are stamped now** (server time, as today); the date on screen is information only | 2026-11-03 |
| D1 | Only the user's locations (`UserWarehouseMapping`, active, company); only active items mapped to that location (`ItemWarehouseMap`) of the chosen inventory type | 2026-11-03 (default) |
| D2 | Count rows are stored in the **main unit** from now on, so the report and consumption read one unit | 2026-11-03 (default) |
| D3 | Old endpoints `AddEditStockCheck`, `SaveStockCheckIndv`, `GetStockCheckItemListMarge` kept for other callers, but company-guarded, permission-checked and routed through the new safe save | 2026-11-03 (default) |

## Out of scope
Count sessions / approvals; backdated counts; correcting stock already wiped by S1 (the pre-check lists suspects); the Stock Check Report screen.

## Found, not fixed
| Item | Note |
|---|---|
| Old procedures | `SP_AddEditStockCheck`, `SP_SaveStockCheckIndvNew` and `SP_AddEdit_Stock` stay in the database with their bugs; the API no longer calls them, but anything else that does (jobs, reports) still can |
| Server-local time | Counts keep `GETDATE()` (server local) because the report and consumption functions compare with local dates; the rest of the app is moving to UTC |
