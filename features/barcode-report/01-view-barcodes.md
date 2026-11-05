# 01 — Barcoded stock

## Summary
Users with `view_barcode_report` see the stock held under barcodes at their locations, by item and barcode, with expiry status and value, and can export it.

## Main flow
1. Report → Barcode Report. Loads on open on the In stock tab.
2. Status tabs with barcode counts: In stock · Expiring soon · Expired · Used up. Filters: search ("Search item or barcode...") · Location ("All my locations") · Inventory type ("All types"). Header button: **Export CSV**.
3. Summary line: "n items · m barcodes · remaining value ₹x" for the filtered result.
4. Columns: ▸ · Item (alias beneath) · Type · Remaining · Barcodes · Nearest expiry (date and status chip) · Value.
5. Expanding shows the barcodes: Barcode · Location · Vendor · Mfg date · Expiry (date and status chip) · Initial · Remaining · Value.
6. Paging footer (items); phone cards with the barcodes inside.

## Alternative flows
Empty: "No barcodes at your locations yet." · No match + Clear filters: "No barcodes match these filters." · An empty status tab with no other filter: "Nothing expiring in the next 30 days." / "No expired barcodes." / "No used-up barcodes." · Error + Retry: "We couldn't load the report." · No location: "You aren't assigned to any location. Ask an admin to add you in User List." · Export with nothing to export: "Nothing to export for these filters." · No permission: "You don't have permission to perform this action."

## Acceptance criteria
1. Mfg and expiry dates always show the right day and month, in the company format (B1).
2. An item's row and its expansion always agree: the row's total and count are the sum of the barcodes shown; a tab never shows an item with an empty expansion (B2).
3. An item with barcodes in main and alternate units shows one row, in the main unit (B3, D4).
4. Status follows D1 exactly; a barcode with no expiry date is never expired or expiring (B4).
5. Only the caller's company and locations, also for the export; a location outside them returns no rows; legacy barcodes with no location follow D2 (B5).
6. Search finds an item by name or alias, and a barcode by number (B6, D3).
7. Value = remaining × unit cost, used-up barcodes count 0; the total equals the sum of the export (Q3).
8. Export CSV matches the filtered screen, one line per barcode (D9).
9. Works at 360–1920 px with all feedback states (`ui-ux.md`).

## Open questions
None.
