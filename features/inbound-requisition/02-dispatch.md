# 02 — Dispatch goods

## Summary
A user with `create_item_transfer` sends goods against an approved requisition from their location. Each dispatch is one transfer; a requisition can be sent in several dispatches.

## Main flow
1. **Dispatch** → page `/admin/inbound-requisition/dispatch?reqId=`. Header: requisition number, date, from (requester) → to (my location), approved by.
2. Lines: Item · Unit · Approved · Sent before (in transit + received) · Remaining · In stock here · **Send** (prefilled with the smaller of remaining and stock; editable; fully sent lines show "Fully sent").
3. Transport: Vehicle * · Driver * (searchable, active) · Responsible person · Phone · Transfer date * (today) · Note.
4. Footer: "n items to send" · Cancel · **Dispatch** → confirm "Dispatch 4 items to Kitchen 1? Stock at Central Store goes down now." → server checks permission, requisition (company, approved, to = a location of the user), date, vehicle / driver, every line (on the requisition, qty > 0, ≤ remaining, ≤ stock), then in one transaction under the number lock and the sender's stock lock: creates `TRF/<FY>/<n>` (From = sender, To = requester, In transit), lines with qty > 0, vehicle relation, and takes the stock (main unit) → "Transfer TRF/2026-27/0001 dispatched" → the existing transfer print dialog opens.

## Alternative flows
"Requisition not found" · "Only approved requisitions can be dispatched." · "Select a vehicle" · "Select a driver" · "Transfer date can't be in the future" / "…before the requisition date" · "Enter a quantity to send for at least one item" · "Quantity must be greater than 0" · "Only 4 kg of Onion left to send" · "Only 2 kg of Onion in stock at Central Store" · "You don't have permission to perform this action."

## Acceptance criteria
1. Stock goes down at the **supplier**; the transfer shows up in Item Receive at the **requester** (I1).
2. Two dispatches at once never lose a deduction; units converted; stock row found by item + location (I2).
3. All checks hold on the server, also for the old endpoint (except vehicle / driver there) (I3).
4. Numbers sequential per company per FY (I4); any failure saves nothing (I6).

## Open questions
None.
