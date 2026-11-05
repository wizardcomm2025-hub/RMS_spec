# 01 — Current stock

## Summary
Users with `view_stock_report` see current stock, value and low-stock status per item at their locations.

## Main flow
1. Report → Stock Report. Loads on open. Type tabs above the filters (All + one per inventory type, each with its item count under the other filters, as in Recipe). Filters: search (item name / alias) · Location (my locations, "All my locations") · Show: All (default) / Low stock / Out of stock. Header buttons: **Export CSV**.
2. Summary line above the table: "n items · total value ₹x" for the filtered result.
3. Columns: ▸ (expand, only when more than one location) · Item (name, alias beneath) · Type · Quantity (main unit) · Reorder level · Status (*Low* / *Out* chip, centred) · Unit cost ₹ · Value ₹.
4. Expanding shows one line per location: Location · Quantity · Status · Value.
5. Paging footer (items); phone cards with the per-location lines inside.

## Alternative flows
Empty: "No stock at your locations yet." · No match + Clear filters · Error + Retry · no location: "You aren't assigned to any location. Ask an admin to add you in User List." · Export with nothing to export: "Nothing to export for these filters."

## Acceptance criteria
1. Stock at a location shows whether or not the item is mapped there (S1); mapped items without stock show 0.
2. Alternate-unit rows converted; every quantity shows its unit (S2); only active stock rows and active, non-deleted locations count (S3).
3. Only the caller's company and locations, also for the per-location lines and the export (S4, Q1).
4. Item total = sum of its location lines; value = quantity × unit cost; the total value matches the export.

## Open questions
None.
