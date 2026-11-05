# 02 — Receive a transfer

## Summary
A user with `receive_item_receive` confirms what arrived on an in-transit transfer to their location. All lines are received in one save; shortages need a reason. A received transfer opens read-only on the same page.

## Main flow
1. **Receive** → page `/admin/item-receive/receive?transferId=`. Header: transfer number, transfer date, from → to, requisition number, vehicle, driver, contact person / phone, note.
2. Lines: Item (pinned) · Unit · Requested (approved on the requisition) · Sent · **Received** (prefilled with Sent, editable) · Short (Sent − Received, shown when > 0) · **Reason** (required when short).
3. Receive date * (today). Footer: "n items · m short" · Cancel · **Receive**.
4. Confirm "Receive TRF/2026-27/0001 at Kitchen 1? Stock goes up now and the transfer can't be changed later." → server checks permission, transfer (company, `To` = a location of the user, still *In transit*), date, every line of the transfer present once, 0 ≤ received ≤ sent, reason when short; then in one transaction under the receiver's stock lock: lines saved (`ReceiveQty`, `ReceiveUOM`, `ReceiveNote`, by / at), stock up at the receiver (main unit), transfer *Received* (by / at) → "Transfer TRF/2026-27/0001 received" → back to the list.
5. Received transfer: same page read-only — Received, Short, Reason, Received by / at; no footer buttons.

## Alternative flows
"Transfer not found" · "This transfer has already been received." · "Receive date can't be in the future" / "…before the transfer date" · "Enter the received quantity for every item" · "Quantity can't be negative" · "Only 4 kg of Onion was sent" · "Enter a reason for the shortage of Onion" · "You don't have permission to perform this action." · "Something went wrong. Please try again."

## Acceptance criteria
1. A transfer is received once: a second save (or a parallel one) is refused and stock goes up once (R1).
2. All checks hold on the server, also for the old / mobile endpoints (reason not forced there) (R2, R7).
3. Stock goes up in the main unit; the stock row's unit never changes; parallel stock changes are never lost (R3).
4. Any failure saves nothing (R5); shortage and reason visible on the received transfer.

## Open questions
None.
