# 01 — View and search receives

## Summary
Users with `viewlist_receive_asset` see the company's receives page by page, newest first.

## Main flow
1. Purchase → PO Receive. Default filter: received this month.
2. Filters: date range (received date, applied on **Apply**), Status (Draft / Posted), Vendor, Location; search (receive no, PO no, invoice no, challan no). Columns: Receive No · Date · PO No · Vendor · Location · Invoice / Challan · Amount · Status · Actions.
3. Actions by status — Draft: View · Edit · Delete; Posted: View.

## Alternative flows
Empty: "No receipts this month. Receive goods against a submitted purchase order." · No match: "No receipts match your filters." + Clear filters · Error: "We couldn't load receipts. Check your connection and try again." + Retry.

## Acceptance criteria
1. Server paging (10 / 25 / 50); only the caller's company (R10, R11).
2. Date filter uses the received date; a half-picked range doesn't reload.
3. Status chip: Draft (default), Posted (success). Amount right-aligned with separators.
4. 360 px: phone cards; no page sideways scroll.

## Open questions
None.
