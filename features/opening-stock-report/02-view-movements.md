# 02 — Movements behind an item

## Summary
From a report row, the user opens the list of ledger movements for that item (and location, when the row is a location line) in the range.

## Main flow
1. **Movements** on an item row → dialog "Onion · Central Kitchen · 01/11/2026 – 04/11/2026".
2. Columns: Date · Location · Type (chip) · Document (e.g. GRN/2026-27/0012, TRF/…, Production #35, Wastage, Stock Check, POS sale, Other) · Quantity (+ green / − red, main unit) · Balance after · By.
3. First line "Opening" with the opening balance; last line "Closing". Up to 500 movements, newest last; "Showing the first 500" when more.

## Alternative flows
No movements: "No movements in these dates." · Error + Retry.

## Acceptance criteria
1. Opening + Σ quantities = closing shown on the report.
2. Only the caller's company and locations.

## Open questions
None.
