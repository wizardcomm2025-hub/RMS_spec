# 01 — Wastage in a period

## Summary
Users with `view_wastage_report` see wastage at their locations in a period, by entry, item or reason, with its value, and can export it.

## Main flow
1. Report → Wastage Report. Loads on open with the last 7 days, in the view last used (Entries the first time).
2. Type tabs with counts: All + one per inventory type. Filters: date range (draft + **Apply**) · search (item name / alias) · Location ("All my locations") · Reason ("All reasons"). Header button: **Export CSV**.
3. Summary line: "n entries · wastage value ₹x" for the filtered result, in every view.
4. Toggle at the top of the table: **Entries | By item | By reason**.
5. Entries: Recorded at · Item (alias beneath) · Type · Location · Quantity · Reason · Value · Recorded by.
6. By item: Item (alias beneath) · Type · Quantity · Entries · Value · Share.
7. By reason: Reason · Entries · Items · Value · Share.
8. Paging footer; phone cards.

## Alternative flows
Empty: "No wastage in this period." · No match + Clear filters: "No wastage matches these filters." · Error + Retry: "We couldn't load the report." · No location: "You aren't assigned to any location. Ask an admin to add you in User List." · Export with nothing to export: "Nothing to export for these filters." · Range over 366 days: "Choose a period of up to one year." · No permission: "You don't have permission to perform this action."

## Acceptance criteria
1. Every active entry shows with its own reason, value and time; two entries of one item show as two rows (W1, W4).
2. Export CSV works and matches the current view and filters (W2, D9).
3. Choosing a location works and only returns that location (W3); only the caller's company and locations, also for the export (W5, D1); a location outside them returns no rows.
4. Cancelled entries never appear or count (D7).
5. Alternate-unit rows converted to the main unit; other units as saved with value "—", left out of totals and of By item's quantity (D4).
6. Value = quantity × unit cost. The total is the same in all three views and equals the sum of the export (Q2). Shares add up to about 100% across all pages.
7. The date range is by calendar day; an entry just after midnight on the day after the end date isn't included (W7).
8. Date and time follow the company format (W7).
9. Works at 360–1920 px with all feedback states (`ui-ux.md`).

## Open questions
None.
