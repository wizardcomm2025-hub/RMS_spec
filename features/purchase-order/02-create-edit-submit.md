# 02 — Create, edit and submit a purchase order

## Summary

A user with `create_purchase_order` creates a PO; with `edit_purchase_order` edits a **draft**. **Save** keeps it as Draft; **Submit** sends it to the vendor flow (notifications as today). Lines can be typed, picked from approved requisition lines, or come from an invoice scan.

## Main flow

1. Pick vendor (active), date, add lines (item, qty, unit, cost, description; requisition lines linked).
2. Save / Submit: the server checks permission, that the PO is the company's and still Draft, the edit token, the vendor, every line (Q5) and requisition links; in one transaction it writes the header, updates kept lines, switches off left-out lines (releasing their requisition links), adds new lines, recomputes the total and assigns the number on first save (Q2).
3. Success: "Purchase order saved as draft" / "Purchase order submitted"; the number is shown.

## Alternative flows

- "Only draft purchase orders can be changed." · "Vendor not available" · "One or more items are not available" · "One or more requisition lines are already on another purchase order" · "Add at least one item" · "Purchase order not found" · "This purchase order was changed by someone else. Reload and try again." · "You don't have permission to perform this action." · line messages ("Quantity must be greater than 0").

## Acceptance criteria

1. Another company's PO id → "Purchase order not found"; nothing changed (D1); another PO's line id → ignored (D2).
2. Editing a Submitted / Cancelled PO through the API → locked message (D3).
3. Numbers `PO/2026-27/0001`, `0002`… without gaps from concurrent saves and never repeated (D4).
4. Removing a line and saving → line off, total right, requisition line pending again; Cancel keeps the PO unchanged (D5).
5. Inactive vendor / item, another company's item, a requisition line on another PO, qty 0 → refused (D6, D7).
6. A failure mid-save leaves the PO as it was (D7).
7. The OCR flow and the requisition panel still add lines as today.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; notifications on submit as today.

## Open questions

None.
