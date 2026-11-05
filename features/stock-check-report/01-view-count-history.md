# 01 — Stock count history

## Summary
Users with `view_stock_check_report` see every count made in a period at their locations and can export it.

## Main flow
1. Report → Stock Check Report. Loads on open with the last 7 days, newest first.
2. Type tabs above the filters: All + one per inventory type, each with its count under the other filters (as in Stock Report). Filters: date range (draft + **Apply**) · search (item name / alias) · Location ("All my locations"). Header button: **Export CSV**.
3. Summary line above the table: "n counts · counted value ₹x" for the filtered result.
4. Columns: Counted at · Item (name, alias beneath) · Type · Location · Counted qty (main unit) · Value ₹ · Counted by · Note.
5. Paging footer; phone cards.

## Alternative flows
Empty: "No stock checks in this period." · No match + Clear filters · Error + Retry · No location: "You aren't assigned to any location. Ask an admin to add you in User List." · Export with nothing to export: "Nothing to export for these filters." · Range over 366 days: "Choose a period of up to one year." · No permission: "You don't have permission to perform this action."

## Acceptance criteria
1. Every active count shows on its calendar day, whether its date was saved with or without a time (R1, D3); two counts of one item the same day show as two rows (R4).
2. Items never counted don't appear; a count shows even if the item is no longer mapped to that location (R3).
3. Only the caller's company and locations, also for the export (R5, D1); a location id outside them returns no rows.
4. Quantity in the main unit with its unit shown; alternate-unit rows converted; other units as saved with value "—" (D4).
5. Value = counted quantity × unit cost; the summary total equals the sum of the export (Q3).
6. Export CSV works and matches the filtered screen, apart from paging (R2, D8).
7. A real count of 0 shows; the zero rows from item creation don't (D6).
8. Date and time follow the company format, with AM / PM (R6).
9. Works at 360–1920 px with all feedback states (`ui-ux.md`).

## Open questions
None.
