# 02 — Needs attention

## Summary
A list of what is waiting right now, each row with a count and a link. Rows with nothing waiting and rows the user has no permission for aren't shown.

## Rows

| Row (verbatim, n = count) | Counted | Permission | Opens |
|---|---|---|---|
| "n requisitions to approve" | Requisitions pending approval (status 2) raised from my locations | `approve_requisition_for_stock_transfer` | Item Requisition |
| "n requisitions to dispatch" | Approved requisitions addressed to my locations with something left to send (Open in Inbound Requisition) | `viewlist_inbound_requisition` | Inbound Requisition |
| "n transfers to receive" | Transfers in transit (status 2) to my locations | `viewlist_item_receive` | Item Receive |
| "n purchase orders waiting for delivery" | Submitted POs not fully received | `viewlist_receive_asset` | PO Receive |
| "n draft purchase orders" | Draft POs | `viewlist_purchase_order` | Purchase Order |
| "n locations with no stock check today" | My locations with no stock count dated today | `viewlist_stock_check` | Stock Check |

Singular wording when n = 1 ("1 transfer to receive").

## Main flow
1. The section loads with the page and follows the location filter; POs are company-wide (D4). The date range does not apply: it is what is waiting now.
2. Rows appear in the order above, with the count as a badge. Clicking a row opens the module.
3. Nothing waiting → "All caught up. Nothing is waiting for you."

## Alternative flows
No allowed rows at all → the section is hidden · load failure → "Couldn't load this. Retry".

## Acceptance criteria
1. Counts have no cap and equal the module screen's matching filter (H1, H4, H7).
2. A count made today counts as done, whatever its time (H5).
3. The location filter is applied (H6), and only my locations count.
4. A row shows only with its permission, checked on the server.

## Open questions
None.
