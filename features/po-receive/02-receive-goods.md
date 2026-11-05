# 02 — Receive goods against a purchase order

## Summary
A user with `receive_receive_asset` records a delivery; with `receive_receive_asset` edits a **draft**. **Save draft** keeps it editable with no stock effect; **Post receipt** adds stock and locks it.

## Main flow
1. **＋ Receive goods** → pick a purchase order (searchable: the company's **Submitted** POs with something left, shown as "PO/2026-27/0004 · Haji Meat Shop").
2. Vendor fills from the PO (read-only). Enter Location *, Received date * (today), Invoice no, Challan no, optional invoice / challan files (pdf, jpg, png ≤ 5 MB).
3. Lines come from the PO: Item · Ordered · Received before · Remaining · **Receiving** (prefilled = remaining) · Damaged · UOM · Unit cost (PO cost, editable) · Tax · Amount. Fully received lines show "Fully received" and can't be changed.
4. Footer: Lines total, Discount, **Receive total** (prefilled, editable; "Differs from lines by 120.00" when changed).
5. **Save draft** → number `GRN/2026-27/0001` on first save; "Receipt saved as draft (GRN/…)".
6. **Post receipt** → confirm "Post receipt GRN/… ? Stock at <location> increases and it can't be edited after this." → server checks everything again inside one transaction under a per-company lock, adds received − damaged to stock in the main unit, records the price, marks it Posted → "Receipt posted (GRN/…)".

## Alternative flows
"Purchase order not found" · "This purchase order can't be received (it's draft, cancelled or fully received)." · "Select a location" · "Received date can't be in the future" / "…before the PO date" · "Enter a received quantity for at least one item" · "Damaged can't be more than received" · "Only 4 kg left on the PO for Onion" · "Onion is in a unit the raw material doesn't use (box)" · "Enter the invoice no or challan no" · "Invoice INV-77 is already recorded for this vendor" · "Posted receipts can't be changed." · "This receipt was changed by someone else. Reload and try again." · "You don't have permission to perform this action."

## Acceptance criteria
1. A receive is always for the caller's company's PO, picked by id, never by number (R1).
2. Any user with the permission can receive and open receives (R2).
3. Stock += Σ (receiving − damaged) converted to the main unit at the chosen location; damaged kept on the line (R3, Q2).
4. Two receives can't together exceed the ordered qty; checked when posting, under a lock (R4, Q3).
5. Saving or posting a Posted receive → "Posted receipts can't be changed."; stock unchanged; another company's receive id → "Receipt not found" (R5).
6. Attached files are stored and shown as links on the receive (R6).
7. Location, vendor = PO vendor, date rules enforced (R7, R8, D3).
8. One set of amounts: line amounts → total (editable) (R9, Q4).
9. A failure mid-post leaves stock and the receive unchanged.

## Platform notes
[`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Last purchase cost on Raw Material keeps reading `Item_Price_History`.

## Open questions
None.
