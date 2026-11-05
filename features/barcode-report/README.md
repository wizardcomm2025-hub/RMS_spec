# Report → Barcode Report

The **Barcode Report** shows the stock held under barcodes at the user's locations: one row per item (remaining quantity, number of barcodes, nearest expiry, value), expanding to each barcode with its location, vendor, dates and quantities. Status tabs separate what's in stock, expiring soon, expired and used up. The result can be exported to CSV, one line per barcode.

Status (2026-11-04): **🟢 Built · SQL not yet run · testing deferred.** Plan: [`RMS_API/specs/barcode-report/plan.md`](../../../RMS_API/specs/barcode-report/plan.md). UI / UX plan: [`ui-ux.md`](./ui-ux.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `BRP`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-barcodes.md`](./01-view-barcodes.md) | See barcoded stock by item and barcode, by expiry status; filter, export |

## Defects this feature fixes (found 2026-11-04)

| # | Defect | Fixed by |
|---|---|---|
| B1 | **Dates can swap day and month**: the procedures send dates as `dd/MM/yy` (or `dd.MM.yyyy`) text and the screen re-reads them as a date, so `01/06/26` shows as 6 January | [01](./01-view-barcodes.md) |
| B2 | **Items with nothing left show "0.00" and an empty expansion**: the list includes used-up barcodes, the detail and export don't | [01](./01-view-barcodes.md) |
| B3 | **An item with barcodes in two units can show a wrong row**: the list groups by unit, but the result model's key is the item and it's read with tracking, so the second row repeats the first; quantities aren't converted to one unit | [01](./01-view-barcodes.md) |
| B4 | **No expiry view**: nothing shows what is expired or about to expire, which is the main reason to track barcodes | [01](./01-view-barcodes.md) |
| B5 | **No location on the screen, and no location rule or permission check on the server** (`[Authorize]`): anyone sees every location's barcodes in the company | [01](./01-view-barcodes.md) |
| B6 | No total count, so the pager guesses; search covers the item name only, not the barcode number; no value | [01](./01-view-barcodes.md) |
| B7 | The help text says the report "generates" barcodes, which it doesn't; old page design | [01](./01-view-barcodes.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **One row per item, expanding to its barcodes**; the export has one line per barcode | 2026-11-04 |
| Q2 | **Status tabs with counts**: In stock (default) · Expiring soon · Expired · Used up; expired and expiring dates highlighted | 2026-11-04 |
| Q3 | **Remaining value with a total**: remaining quantity × the item's unit cost, in the company's currency | 2026-11-04 |
| D1 | Status of a barcode, by today's date: *Used up* = remaining ≤ 0; *Expired* = remaining > 0 and expiry before today; *Expiring soon* = remaining > 0 and expiry within the next 30 days, today included; *In stock* = every barcode with remaining > 0, so it includes expired and expiring ones. A barcode with no expiry date is never expired or expiring | 2026-11-04 (default) |
| D2 | **Own locations only**, enforced on the server. Legacy barcodes with no location show with Location "—" to anyone with the permission, as the Wastage scan treats them, and are left out when a location is chosen | 2026-11-04 (default) |
| D3 | Filters: search (item name, alias or barcode number) · Location · Inventory type. No date range: the report shows the current state. A tab or filter shows an item with only its matching barcodes; searching a barcode number shows that barcode, searching an item shows all its barcodes in the tab | 2026-11-04 (default) |
| D4 | Item's remaining is in the main unit; alternate-unit barcodes are divided by the conversion. A barcode in any other unit shows as saved with value "—", and is left out of the item's total and of the value total. Barcode lines show the quantity in the barcode's own unit | 2026-11-04 (default) |
| D5 | Unit cost = `fn_GetItemUnitCost` (**today's** cost, as Stock Report) | 2026-11-04 (default) |
| D6 | Order: In stock and Used up by item name; Expiring soon and Expired by nearest expiry first. Barcodes within an item: soonest expiry first, those without a date last | 2026-11-04 (default) |
| D7 | Only active barcodes of the company's active, non-deleted items, as today | 2026-11-04 (default) |
| D8 | Permission `view_barcode_report` for the report and export | 2026-11-04 (default) |
| D9 | Export: CSV of every barcode in the filtered result (no paging, cap 50 000) | 2026-11-04 (default) |
| D10 | `/Setting/TotalQtyOfBarcodeReport`, `GetBarcodeReportByItemId`, `GetBarcodeExportReport` are kept for other callers, with `[CompanyAuthorize]` + permission | 2026-11-04 (default) |

## Known limits
Value uses today's cost, so a barcode's value changes when the cost changes (D5). The 30-day window is fixed. Remaining quantity is only as accurate as the screens that reduce it (Wastage scan and others).

## Out of scope
Generating or printing barcodes (Bar-code Generation); expiry alerts or e-mails; a configurable expiry window; movement history of a barcode; correcting a barcode's quantity.
