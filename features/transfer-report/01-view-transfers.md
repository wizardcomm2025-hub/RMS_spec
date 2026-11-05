# 01 — Transfers in a period

## Summary
Users with `view_transfer_report` see the transfers at their locations in a period, what was sent, what arrived and what was short, and can export them.

## Main flow
1. Report → Transfer Report. Loads on open with the last 7 days, newest first.
2. Status tabs with counts: All · In transit · Received · With shortage. Filters: date range (draft + **Apply**) · search (transfer number, requisition number, item) · Location ("All my locations"). Header button: **Export CSV**.
3. Summary line: "n transfers · sent ₹x · short ₹y" for the filtered result.
4. Columns: ▸ · Transfer (number, requisition number beneath) · Date · From → To · Sent by · Received by (date beneath) · Status (centred chip) · Sent value · Short value.
5. Expanding shows the item lines: Item (alias beneath) · Sent · Received · Short · Sent value · Short value · Reason.
6. Paging footer (transfers); phone cards with the lines inside.

## Alternative flows
Empty: "No transfers in this period." · No match + Clear filters: "No transfers match these filters." · Error + Retry: "We couldn't load the report." · No location: "You aren't assigned to any location. Ask an admin to add you in User List." · Export with nothing to export: "Nothing to export for these filters." · Range over 366 days: "Choose a period of up to one year." · No permission: "You don't have permission to perform this action."

## Acceptance criteria
1. Sent by is the person who created the transfer; Received by is the person who received it, blank until then (T1, T2).
2. An in-transit transfer shows Received and Short as "—", isn't in *With shortage* and adds nothing to the short total (T3, D4).
3. The date filter and the date shown are both `TransferDate`, by calendar day (T4).
4. Only the caller's company and transfers with either end at the caller's locations, also for the export (T5, Q2); a location outside them returns no rows.
5. Inactive transfers and lines don't count; a transfer with no active line doesn't show (T6).
6. Value: alternate-unit lines converted; other units show "—" and are left out of totals (D6). A transfer's values = the sum of its lines; the totals equal the sum of the export (Q3).
7. A legacy over-receipt shows as "over" and isn't a shortage (D5).
8. Export CSV matches the filtered screen, one line per item (D10).
9. Date and time follow the company format (T8).
10. Works at 360–1920 px with all feedback states (`ui-ux.md`).

## Open questions
None.
