# 01 — Transfers to receive

## Summary
Users with `viewlist_item_receive` see transfers coming to their locations.

## Main flow
1. Stock → Item Receive. Filters: search (transfer or requisition number) · date range (transfer date, Apply; default this month) · My location (to; "All my locations") · From (sender) · Show: **In transit** (default) / Received / All.
2. Columns: Transfer No · Date · From → To · Requisition No · Items · Vehicle / Driver · Status (*In transit* / *Received* chip; *Received · 2 short* when lines were short) · Received (date · by) · Actions (**Receive** when in transit and `receive_item_receive`; **View** when received).
3. Paging footer; phone cards; loads on open (no "Get Items" button).

## Alternative flows
Empty in transit: "Nothing on the way to your locations." · No match + Clear filters · Error + Retry · no location: "You aren't assigned to any location. Ask an admin to add you in User List."

## Acceptance criteria
1. Only the caller's company; `To` in the user's locations (R4).
2. "All" locations works; From lists senders; inactive locations still show their name (R6).
3. *Received* columns are empty for transfers in transit (R6).

## Open questions
None.
