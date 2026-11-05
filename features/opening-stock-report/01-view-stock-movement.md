# 01 — Stock movement (opening → closing)

## Summary
Users with `view_opening_stock_report` see, per item at their locations, opening, in, out, adjustments and closing for a date range.

## Main flow
1. Report → Opening Stock Report. Loads for today. Type tabs with counts. Filters: date range (Apply; no future; not before the ledger start) · search · Location (my locations) · Show: All / With movement / Negative closing. Header: **Export CSV**.
2. Summary: "n items · opening ₹x → closing ₹y" (values at today's unit cost). When the range starts before the ledger: notice "Stock history starts on {date}; figures start from that day."
3. Columns: ▸ (more than one location) · Item (type · location caption) · Opening · In · Out · Count adj. · Closing (negative in red) · Closing value ₹ · Actions (**Movements**).
4. Expanding shows the same columns per location.
5. Paging footer; phone cards.

## Alternative flows
Empty: "No stock movement at your locations for these dates." · No match + Clear filters · Error + Retry · no location message · future dates not selectable.

## Acceptance criteria
1. Opening + In − Out + Count adj. = Closing for every line (to 3 decimals).
2. Closing for *to* = today equals the Stock Report quantity (same main-unit conversion).
3. Only the caller's company and locations; items with stock but no mapping included.
4. A movement dated on a day belongs to that day (business date), whenever it was saved.

## Open questions
None.
