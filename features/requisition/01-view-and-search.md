# 01 — View and search requisitions

## Summary
Users with `viewlist_requisition_for_stock_transfer` see requisitions raised **from** their locations, newest first.

## Main flow
1. Stock → Requisition. Filters: date range (requisition date, Apply; default this month) · Status (Pending approval / Approved / Declined / Cancelled / In transit / Received — from `RequisitionStatus`) · From (user's locations) · To · search (requisition number).
2. Columns: Requisition No · Date · From → To · Items · Created by · Approved / declined by · Status · Transfers (in transit / received counts, click → transfer list) · Actions.
3. Actions by status — Pending: View · Edit · Cancel; others: View.

## Alternative flows
Empty: "No requisitions this month. Raise one to request items from another location." · No match + Clear filters · Error + Retry.

## Acceptance criteria
1. Server paging; caller's company; from-location in the user's locations (R8, R9).
2. Search finds by number (old and new style).
3. Status chip colours: Pending (warning), Approved (info), Declined / Cancelled (error / default), Received (success).
4. 360 px: phone cards.

## Open questions
None.
