# 01 — Inbound requisitions

## Summary
Users with `viewlist_inbound_requisition` see approved requisitions addressed to their locations.

## Main flow
1. Stock → Inbound Requisition. Filters: My location (to; default first, "All my locations" allowed) · date range (requisition date, Apply; default this month) · From (requester) · Show: Open (default) / Fully sent / All · search (requisition number).
2. Columns: Requisition No · Date · From → To · Items · Approved by · Left to send (items with something left, e.g. "3 of 5") · Transfers (in transit / received counts, click → transfer list) · Actions (**Dispatch** when open and `create_item_transfer`; view transfers).
3. Footer with paging; phone cards.

## Alternative flows
Empty open: "Nothing waiting to be sent from your locations." · No match + Clear filters · Error + Retry · no location: "You aren't assigned to any location."

## Acceptance criteria
1. Only the caller's company; `To` in the user's locations (I5).
2. Declined / cancelled / pending requisitions never appear.
3. Left to send matches the dispatch page.

## Open questions
None.
