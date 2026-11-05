# 03 — Cancel a submitted purchase order

## Summary

A user with `edit_purchase_order` cancels a **Submitted** PO that has **nothing received** (Q1), with a reason.

## Main flow

1. Cancel → "Cancel purchase order": "Cancel **PO/2026-27/0004**? The vendor order is withdrawn and its requisition lines become pending again. This can't be undone." + Reason * → "Purchase order cancelled".
2. The PO becomes **Cancelled** (status log with the reason); requisition lines released.

## Alternative flows

- "This purchase order has goods received against it and can't be cancelled." · "Only draft purchase orders can be changed." (draft — delete it instead) · "Enter a reason for cancelling" · "Purchase order not found" · "You don't have permission to perform this action.".

## Acceptance criteria

1. Submitted, nothing received → Cancelled; requisition lines pending.
2. Any receipt (draft or final) → refused.
3. Draft → refused (use Delete). Another company's PO → not found.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; notifications on submit as today.

## Open questions

None.
