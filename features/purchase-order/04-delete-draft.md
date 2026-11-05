# 04 — Delete a draft purchase order

## Summary

A user with `edit_purchase_order` deletes a **Draft** PO (Q3).

## Main flow

1. Delete → "Delete purchase order": "Delete draft **PO/2026-27/0005**? This can't be undone." → "Purchase order deleted".
2. Header and lines marked deleted; requisition lines released.

## Alternative flows

- "Only draft purchase orders can be changed." (not a draft) · "Purchase order not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Draft → gone; requisition lines pending.
2. Submitted / Cancelled → refused. Another company's PO → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; notifications on submit as today.

## Open questions

None.
